# Casos de uso.

## Concepto Transversal: Política de Notificación (`Notification Policy`)

Antes de detallar los casos de uso, es fundamental establecer que el núcleo de este contexto no es simplemente "enviar mensajes". Toda notificación o alerta en el ecosistema AxolPass se rige por una **Política de Notificación** compuesta de manera unificada por:

* **Resolución de Destinatarios:** Identificación dinámica de a quién va dirigido el mensaje, interactuando con el Contexto de Comunidad.
* **Preferencias del Usuario (Opt-out):** El respeto estricto a las configuraciones de silencio o granularidad del receptor.
* **Prioridad y Caducidad (TTL):** La urgencia del mensaje (estándar vs. crítica) y cuánto tiempo tiene sentido intentar entregarlo antes de que su contenido pierda valor.
* **Canal de Entrega:** El medio utilizado para el despacho (Push, WhatsApp, Correo Electrónico).
* **Estrategia de Reintentos:** Las reglas de resiliencia frente a caídas de proveedores externos (ej. *Exponential Backoff* vs. reintentos agresivos).
* **Mantenimiento de Tokens:** La limpieza proactiva de dispositivos o contactos inválidos.

Hacer explícita esta política asegura que el motor de notificaciones trate a todos los mensajes de forma predecible, escalable y auditable.

## 1. Alertas Operativas y de Seguridad (Tiempo Real)

### UC-NOT-01: Notificar Acceso a Residente (`DispatchAccessAlertUseCase`)

* **Actor:** Sistema (Ejecución asíncrona en respuesta a los eventos `AccessGrantedEvent` o `UnscheduledVisitAlertEvent` del Contexto de Accesos).
* **Descripción:** Flujo transaccional que alerta en tiempo real a los residentes de una unidad privativa que una visita o proveedor ha cruzado la caseta. Actúa como un consumidor de eventos que consulta al Contexto de Comunidad para resolver los destinatarios, recupera los tokens de sus dispositivos móviles y delega la entrega del mensaje Push a la infraestructura externa en la nube (ej. FCM o APNs), **conforme a lo establecido en el ADR-012**.
* **Comando de Entrada:** `DispatchAccessAlertCommand`
  * `houseId` (ID de la casa destino).
  * `visitorName` (Nombre del visitante).
  * `visitType` (El tipo de visita: `PEDESTRIAN`, `VEHICLE`, `RIDE_HAILING`, `DELIVERY`).
  * `accessMethod` (QR escaneado, Apertura Manual, etc.).
  * `timestamp` (Momento exacto del acceso).
* **Salida Esperada:** Confirmación de encolamiento y despacho hacia el proveedor externo.
* **Eventos Disparados:** `PushNotificationDispatchedEvent` o `PushNotificationFailedEvent`.

* **Reglas de Negocio (Invariantes):**
  * *RN-NOT-01.1 - Semántica Dinámica por Tipo de Visita:* El motor de plantillas de notificaciones no utilizará un texto estático. Evaluará el `visitType` inyectado en el comando para generar un mensaje contextualmente rico que indique al residente la acción esperada. Ejemplos de salida del motor:
    * Si es `DELIVERY`: "Tu pedido o entrega (Juan Pérez) ya está dentro del fraccionamiento y va en camino a tu puerta."
    * Si es `RIDE_HAILING`: "Tu transporte (Uber/Didi) ha ingresado al fraccionamiento, por favor sal a su encuentro."
    * Si es `VEHICLE` o `PEDESTRIAN`: "Tu visita (Juan Pérez) acaba de registrar su ingreso en caseta."
  * *RN-NOT-01.2 - Resolución de Destinatarios:* El sistema consultará al Contexto de Comunidad enviando el `houseId` para obtener los identificadores internos de los residentes principales y secundarios con permisos activos en esa casa.
  * *RN-NOT-01.3 - Tolerancia a Fallos y Reintentos:* Si el proveedor externo de notificaciones Push falla o agota el tiempo de espera, el sistema aplicará una política de reintento con retroceso exponencial (*exponential backoff*) para no bloquear recursos.
  * *RN-NOT-01.4 - Caducidad del Mensaje (TTL):* Dado el contexto físico del fraccionamiento, se aplicará un tiempo de vida máximo muy corto (ej. 2 a 3 minutos) a la alerta. Si el mensaje no logra despacharse en esa ventana por problemas de red, la notificación Push activa será descartada para evitar confusión en el residente, registrándose el evento únicamente en su historial de la app.
  * *RN-NOT-01.5 - Mantenimiento de Tokens:* Si el proveedor externo reporta que un identificador de dispositivo es inválido (ej. el residente desinstaló la aplicación), el sistema ejecutará la limpieza automática purgando ese token de la base de datos.
  * *RN-NOT-01.6 - Respeto de Preferencias:* El sistema validará la configuración del residente, omitiendo el envío del Push si este decidió silenciar las alertas rutinarias de acceso.

* **Consideraciones / Edge Cases:**
  * *Desacoplamiento Estricto:* La ejecución de este caso de uso jamás debe bloquear la respuesta HTTP que levanta la pluma en la caseta. Todo ocurre en un hilo en segundo plano (Background Worker o Message Broker).


### UC-NOT-02: Despachar Alerta Crítica de Seguridad (`DispatchCriticalAlertUseCase`)

* **Actor:** Sistema (Ejecución asíncrona y de máxima prioridad en respuesta al evento `EmergencyOverrideTriggeredEvent` del Contexto de Accesos).
* **Descripción:** Flujo de emergencia encargado de distribuir una alerta inmediata cuando un guardia detona el Botón de Pánico en la caseta. El sistema identifica a los responsables de seguridad y administración del fraccionamiento, ignorando las preferencias de silencio, y despacha notificaciones Push con banderas de máxima prioridad hacia la infraestructura externa (FCM/APNs), conforme al ADR-012.
* **Comando de Entrada:** `DispatchCriticalAlertCommand`
  * `communityId` (ID del fraccionamiento).
  * `deviceId` / `guardId` (Identificador de la tablet o del guardia en turno, si hay sesión activa).
  * `location` (Nombre de la caseta o acceso donde se originó, ej. "Caseta Principal").
  * `timestamp` (Hora exacta de la detonación).
* **Salida Esperada:** Confirmación de despacho inmediato y con prioridad alta hacia el proveedor externo.
* **Eventos Disparados:** `CriticalAlertDispatchedEvent` (Para el registro inmutable en el Contexto de Auditoría).

* **Reglas de Negocio (Invariantes):**
  * *RN-NOT-02.1 - Resolución de Destinatarios de Emergencia:* El sistema consultará los Contextos de Comunidad e IAM enviando el `communityId` para obtener exclusivamente los identificadores de los usuarios con roles definidos como responsables de respuesta operativa inmediata para esa comunidad (ej. administradores, guardias, comité de vigilancia). Los residentes generales no reciben esta alerta para evitar pánico generalizado.
  * *RN-NOT-02.2 - Bypass de Preferencias (No-Opt-Out):* Esta notificación ignora cualquier configuración de silencio o "No Molestar" que el administrador haya definido en la app. El *payload* hacia FCM/APNs se construirá forzando los parámetros de máxima prioridad (ej. `priority: high` y atributos de sonido de emergencia para despertar el dispositivo).
  * *RN-NOT-02.3 - Caducidad Extendida (TTL Crítico):* A diferencia de los avisos de visita, la alerta de emergencia tendrá un tiempo de vida (TTL) extendido en el servidor de push (ej. 30 a 60 minutos). Si un administrador está temporalmente sin señal (ej. en un elevador o sótano), la alerta debe entregar de inmediato al recuperar la conexión.
  * *RN-NOT-02.4 - Reintentos Agresivos:* Si el proveedor externo falla (errores 500), la política de reintentos no tendrá un retroceso exponencial lento, sino reintentos continuos a intervalos cortos hasta confirmar la recepción por el servidor en la nube.

* **Consideraciones / Edge Cases:**
  * *Protección de Ráfagas (Debouncing/Rate Limiting):* Como acordamos en el ADR-011, el botón de pánico en la tablet puede funcionar sin sesión de usuario. Si en el nerviosismo el guardia presiona el botón 15 veces en 3 segundos, este caso de uso debe colapsar esos eventos en una sola alerta crítica para no saturar a los administradores ni ser bloqueados por *Spam* en FCM/APNs.


### UC-NOT-03: Notificar Salida a Residente (`DispatchExitAlertUseCase`)

* **Actor:** Sistema (Ejecución asíncrona en respuesta al evento `DepartureRegisteredEvent` del Contexto de Accesos).
* **Descripción:** Flujo transaccional que notifica al residente cuando un visitante o proveedor ha registrado su salida en la caseta. Provee paz mental, facilita el control de tiempos de estadía de contratistas y actúa como un mecanismo de seguridad distribuida. *Nota arquitectónica: Este flujo reutiliza textualmente la misma estrategia base de resolución de destinatarios, mantenimiento de tokens y despacho del UC-NOT-01, diferenciándose únicamente en las preferencias granulares de silencio y en un TTL más holgado.*
* **Comando de Entrada:** `DispatchExitAlertCommand` 
  * `unitId` (ID de la casa).
  * `visitorName` (Nombre registrado).
  * `visitorProfile` (Tipo: `FAMILY`, `FRIEND`, `PROVIDER`, `DELIVERY`).
  * `durationInMinutes` (Tiempo total que permaneció dentro del fraccionamiento).
  * `timestamp` (Hora de salida).
* **Salida Esperada:** Confirmación de despacho hacia FCM/APNs.
* **Eventos Disparados:** `ExitPushDispatchedEvent`.

* **Reglas de Negocio (Invariantes):**
  * *RN-NOT-03.1 - Preferencias Granulares (Opt-out Específico):* A diferencia del UC-NOT-01, el sistema permitirá al residente configurar sus alertas de salida segmentadas por perfil de visita. Por ejemplo, puede silenciar las salidas de `FAMILY`, pero mantener obligatorias las alertas para `PROVIDER`.
  * *RN-NOT-03.2 - Resolución y Mantenimiento Base:* Este caso de uso hereda obligatoriamente las reglas de resolución delegada de destinatarios (RN-NOT-01.1) y de purga de tokens inválidos (RN-NOT-01.4).
  * *RN-NOT-03.3 - Tolerancia a Fallos y TTL Moderado:* Se aplica la misma resiliencia (*exponential backoff*), pero el tiempo de vida (TTL) del mensaje será mayor al de entrada (ej. 15 a 30 minutos), ya que recibir una confirmación de salida ligeramente demorada sigue aportando valor para el control de la casa sin generar falsa urgencia.

* **Consideraciones / Edge Cases:**
  * *Salidas Omitidas:* Si el guardia olvida registrar la salida de un proveedor, el residente no recibirá esta alerta, lo cual (irónicamente) también es una señal de advertencia para el residente de que el proveedor sigue adentro o el guardia cometió un error operativo.


## 2. Mensajería Transaccional (Comunicación Externa)

### UC-NOT-04: Distribuir Pase de Acceso a Visitante (`DistributeAccessPassUseCase`)

* **Actor:** Sistema (Ejecución asíncrona en respuesta a los eventos `InvitationGeneratedEvent` o `InvitationContactUpdatedEvent` del Contexto de Accesos).
* **Descripción:** Flujo encargado de entregar la invitación formal al visitante externo a través de los canales autorizados. El sistema adapta el nivel de detalle del mensaje dependiendo del canal elegido (Correo Electrónico o WhatsApp), asegurando que el visitante siempre reciba la imagen nativa del Código QR, el código numérico de respaldo (NIP) y las instrucciones de llegada, delegando el envío a proveedores externos (ej. SendGrid o Meta API).
* **Comando de Entrada:** `DistributeAccessPassCommand`
  * `invitationId` (ID único de la invitación).
  * `visitorName` (Nombre del invitado).
  * `residentName` (Nombre del residente que invita).
  * `communityName` (Nombre del fraccionamiento).
  * `contactMethod` (Canal seleccionado: `EMAIL` o `WHATSAPP`).
  * `contactAddress` (El correo o número de teléfono destino).
  * `qrImageData` (El archivo o buffer de la imagen del Código QR).
  * `numericCode` (Código numérico de respaldo de 4 a 6 dígitos).
* **Salida Esperada:** Confirmación de encolamiento y respuesta exitosa de recepción por parte del proveedor.
* **Eventos Disparados:** `AccessPassDispatchedEvent` o `AccessPassDeliveryFailedEvent`.

* **Reglas de Negocio (Invariantes):**
  * *RN-NOT-04.1 - Canales Estrictos y Sin Enlaces:* El envío saliente se restringe a WhatsApp y Correo Electrónico. Toda la información necesaria y el Código QR deben entregarse dentro del mensaje mismo; **el sistema no generará ni enviará enlaces web (URLs) externos** para consultar el pase.
  * *RN-NOT-04.2 - Estrategia de Contenido Directo:*
    * **Para EMAIL (Enriquecido):** Se utilizará una plantilla HTML que incrustará la imagen del Código QR (`qrImageData`) directamente en el cuerpo del mensaje, mostrando el `numericCode` visible, instrucciones detalladas de llegada y el reglamento del fraccionamiento.
    * **Para WHATSAPP (Directo y Multimedia):** Se enviará una plantilla tipo *Media Template* autorizada por Meta, entregando el Código QR nativamente como una imagen en el chat, acompañada de un texto conciso que incluya el saludo y el `numericCode` en texto plano.
  * *RN-NOT-04.3 - Respaldo Numérico Obligatorio:* Todo mensaje saliente debe incluir el `numericCode` explícitamente, garantizando el acceso si el lector óptico de la caseta falla o si la pantalla del teléfono del visitante está dañada.
  * *RN-NOT-04.4 - Delegación y Entregabilidad:* El despacho se realizará mediante APIs de proveedores externos autorizados, protegiendo la reputación del dominio de AxolPass.
  * *RN-NOT-04.5 - Estrategia Inteligente de Reintentos (Fail-Fast vs. Backoff):* El sistema deberá evaluar el código de respuesta del proveedor externo (Meta/Twilio/SendGrid).
    * Errores Transitorios (HTTP 5xx o 429 Rate Limit): El sistema aplicará una política de Exponential Backoff encolando el mensaje para un reintento posterior.
    * Errores Permanentes (Fail-Fast - HTTP 400, Contacto Inválido, Opt-Out): El sistema abortará inmediatamente los reintentos para no desperdiciar recursos, marcando la tarea como fallida.
  * *RN-NOT-04.6 - Emisión de Fallo Definitivo:* Si el proveedor devuelve un error permanente (según la RN-NOT-04.5), el sistema registrará la notificación interna como FAILED y publicará inmediatamente el evento de integración AccessPassDeliveryFailedEvent (conteniendo invitationId, recipientContact y failureReason) hacia el bus de mensajes, delegando el manejo del estado del negocio al contexto correspondiente.

### UC-NOT-05: Notificar Cancelación de Pase de Acceso (`DistributeAccessPassCancellationUseCase`)

* **Actor:** Sistema (Ejecución asíncrona en respuesta al evento `InvitationCanceledEvent` del Contexto de Accesos).
* **Descripción:** Flujo asíncrono que se ejecuta cuando una invitación es anulada antes de ser utilizada. El sistema procesa el evento de cancelación, identifica el canal de contacto del visitante y despacha una alerta para informarle que su código de acceso ya no es válido, evitando que se traslade a la caseta inútilmente.
* **Comando de Entrada:** `DistributeAccessPassCancellationCommand`
  * `invitationId` (ID de la invitación cancelada).
  * `visitorName` (Nombre del invitado).
  * `residentName` (Nombre del residente).
  * `communityName` (Nombre del fraccionamiento).
  * `contactMethod` (Canal original utilizado: `EMAIL` o `WHATSAPP`).
  * `contactAddress` (El correo o número de teléfono destino).
* **Salida Esperada:** Confirmación de despacho hacia el proveedor externo (SendGrid/Meta API).
* **Eventos Disparados:** `CancellationPassDispatchedEvent`.

* **Reglas de Negocio (Invariantes):**
  * *RN-NOT-05.1 - Alineación de Estados del Dominio (Mapeo Estricto):* El motor de notificaciones responderá únicamente a los estados y eventos oficiales emitidos por el contexto de Control de Accesos. Se elimina cualquier referencia a estados obsoletos (`REVOKED`, `USED`). El mapeo semántico para alertas de cancelación se acota de la siguiente manera:
    * `CANCELED`: Detona esta notificación de forma directa al visitante (el residente anuló el pase de forma voluntaria).
    * `CANCELED_BY_ADMIN`: Detona esta notificación de forma directa indicando un motivo administrativo (la casa fue suspendida operativamente por morosidad mediante el UC-ACC-21).
  * *RN-NOT-05.2 - Exclusión de Notificación por Flujos de Cierre:* Las invitaciones que transicionen a los estados `COMPLETED` (visita concluida), `EXPIRED` (no-show) o `DELIVERY_FAILED` (error de entrega) no dispararán este caso de uso, ya que no corresponden a una cancelación activa que requiera alertar al visitante.
  * *RN-NOT-05.3 - Respeto del Canal Original:* El sistema enviará el aviso de cancelación por el mismo canal (WhatsApp o Correo) que se utilizó originalmente para enviar la invitación, garantizando que el mensaje llegue al hilo de conversación correcto.
  * *RN-NOT-05.4 - Claridad del Mensaje:* El texto (o la plantilla HSM en WhatsApp) debe ser directo, indicando claramente que el código numérico y el código QR recibidos previamente ya no son válidos para ingresar a las instalaciones.
  * *RN-NOT-05.5 - Ausencia de Adjuntos:* A diferencia del envío original, este mensaje será exclusivamente de texto plano (en WhatsApp) o HTML simple (en Email). No se adjuntarán imágenes ni códigos.
  * *RN-NOT-05.6 - Garantía de Secuencialidad (Condición de Carrera):* Para evitar que un visitante reciba el aviso de cancelación antes que la invitación original debido a latencias asimétricas en la red, el sistema de mensajería (Message Broker) debe garantizar un procesamiento estrictamente ordenado (FIFO - First-In, First-Out) agrupado por `invitationId`. Un evento de cancelación (UC-NOT-05) jamás debe ser despachado al proveedor si aún existe un evento de distribución (UC-NOT-04) pendiente en la cola para ese mismo identificador.


### UC-NOT-06: Despachar Reenvío de Pase de Acceso (`DispatchAccessPassResendUseCase`)

* **Actor:** Sistema (Ejecución asíncrona en respuesta a la solicitud manual del residente desde su app, generando el evento `InvitationResendRequestedEvent`).
* **Descripción:** Flujo ejecutor de despacho que permite enviar nuevamente una invitación al visitante externo. Actúa de forma estrictamente subordinada: es invocado únicamente después de que el Contexto de Accesos validó la autorización, la vigencia del pase y los límites de reenvío (Rate Limiting). Este proceso asegura que el visitante reciba exactamente el mismo Código QR y código numérico (NIP) original a través del canal especificado, facilitando la recuperación del pase.
* **Comando de Entrada:** `DispatchAccessPassResendCommand`
  * `invitationId` (ID único de la invitación activa).
  * `visitorName` (Nombre del invitado).
  * `residentName` (Nombre del residente que invita).
  * `communityName` (Nombre del fraccionamiento).
  * `contactMethod` (Canal seleccionado para el reenvío: `EMAIL` o `WHATSAPP`).
  * `contactAddress` (El correo o número de teléfono destino final).
  * `qrImageData` (El archivo o buffer de la imagen del Código QR).
  * `numericCode` (Código numérico de respaldo de 4 a 6 dígitos).
* **Salida Esperada:** Confirmación de encolamiento y respuesta exitosa del proveedor externo.
* **Eventos Disparados:** `AccessPassResendDispatchedEvent` o `AccessPassResendFailedEvent`.

* **Reglas de Negocio (Invariantes):**
  * *RN-NOT-06.1 - Reutilización de Plantillas Directas:* Este proceso reutilizará exactamente las mismas plantillas sin enlaces web definidas en el `UC-NOT-04` (Plantilla HTML con imagen incrustada para Email, y *Media Template* para WhatsApp).
  * *RN-NOT-06.2 - Delegación de Validaciones (Trust Boundary):* El motor de notificaciones asume que la petición es válida. La validación estricta de vigencia (que la invitación no esté cancelada, utilizada o expirada) es responsabilidad exclusiva del Contexto de Accesos antes de emitir el evento desencadenante.
  * *RN-NOT-06.3 - Delegación de Rate Limiting:* El control y bloqueo para evitar abusos (ej. limitar a 3 reenvíos por hora para proteger costos de infraestructura) recae íntegramente en el Contexto de Accesos. Este caso de uso simplemente procesará el comando asumiendo que la cuota de reenvíos está intacta.


#### UC-NOT-07: Notificar Cambio de Estado de Suscripción (DispatchSubscriptionStatusChangeUseCase)

* **Actor:** Sistema (Flujo reactivo asíncrono, disparado por el `SubscriptionStatusChangedEvent` emitido desde el Contexto de Suscripción).
* **Descripción:** Este proceso centraliza la comunicación hacia la administración local ante cualquier mutación en el ciclo de vida del contrato B2B de su fraccionamiento. Su propósito es garantizar la transparencia operativa, informando al administrador sobre suspensiones (Blackout Comercial), reactivaciones o la baja definitiva del servicio, evitando que dichas transiciones se perciban como fallas técnicas en la caseta.
* **Comando de Entrada:** `DispatchStatusChangeCommand` (Construido a partir del payload del evento).
* `communityId` (Identificador del fraccionamiento afectado).
* `previousStatus` (Estado anterior).
* `newStatus` (El nuevo estado operativo: `ACTIVE`, `SUSPENDED`, o `INACTIVE`).
* `reason` (Motivo de la transición, útil para plantillas de error o cobranza).
* `effectiveDate` (Timestamp de cuándo entró en vigor el cambio).


* **Salida Esperada:** Despacho exitoso de la alerta al canal o canales oficiales del administrador.
* **Eventos Disparados:**
* `AdminAlertDispatchedEvent` (En caso de éxito).
* `AdminAlertFailedEvent` (En caso de falla persistente tras agotar política de reintentos).


* **Reglas de Negocio (Invariantes):**
* **RN-NOT-07.1 - Resolución Obligatoria de Destinatario:** El motor de notificaciones debe resolver todas las cuentas activas con rol `CommunityAdmin` asociadas al `communityId`, consultando la fuente de verdad correspondiente del ecosistema **IAM/comunidad**.
* **RN-NOT-07.2 - Inmunidad a Preferencias (Opt-out Override):** Debido a las implicaciones legales y operativas de un apagón comercial, este tipo de aviso se clasifica como *Alerta Administrativa Obligatoria*. Ignorará cualquier configuración de "silenciar notificaciones" (`NotificationPreference`) que el administrador haya establecido en su perfil.
* **RN-NOT-07.3 - Enrutamiento Dinámico por Estado (Plantillas):**
* Si `newStatus` es `SUSPENDED`, se utilizará la plantilla de contingencia, informando que la generación de invitaciones está bloqueada pero las emergencias y salidas siguen operativas (según `ADR-006`).
* Si `newStatus` es `ACTIVE` (proveniente de un estado previo no activo), se utilizará la plantilla de restablecimiento de servicio normal.
* Si `newStatus` es `INACTIVE`, se enviará el aviso de terminación de contrato, alertando sobre el cierre operativo definitivo del fraccionamiento y la revocación de privilegios de escritura/administración, de acuerdo con las políticas de retención de datos vigentes.
* **RN-NOT-07.4 - Canal Primario Obligatorio:** Las transiciones de estado de suscripción deben despacharse por **Correo Electrónico** como canal primario ineludible, para mantener un registro auditable fuera del ecosistema móvil. Las notificaciones Push pueden usarse como canal complementario (secundario), pero nunca como el único canal de entrega para este flujo.
* **RN-NOT-07.5 - Idempotencia de Despacho:** Si el mismo cambio de estado ya fue procesado para la misma comunidad y la misma effectiveDate/eventId, el sistema no debe duplicar el envío administrativo.
* **RN-NOT-07.6 - Fan-out Administrativo:** Si existen múltiples CommunityAdmin activos para una misma comunidad, la alerta se despachará a todos ellos.


> Nota: El aviso administrativo no desbloquea ni modifica el estado del sistema; solo comunica el efecto ya vigente.



