# Casos de uso.

## 1. Gestión de Invitaciones (Resident Flow)

### UC-ACC-01: Generar Invitación de Acceso (`GenerateInvitationUseCase`)

* **Actor:** `PrimaryResident` o `SecondaryResident` (operando desde la App Móvil o Web).
* **Descripción:** Flujo central que orquesta la creación de una nueva invitación. El sistema recibe los datos del visitante, valida mediante el *Community Context* que el residente tenga permisos activos y que la casa no esté suspendida. Si la validación es exitosa, el sistema crea un registro de `Invitation` en estado `PENDING` y genera un `AccessToken` (QR/PIN) firmado criptográficamente.
* **Comando de Entrada:** `GenerateInvitationCommand` (`residentId`, `houseId`, `guestName`, `visitType` [PEDESTRIAN, VEHICLE, RIDE_HAILING, DELIVERY], `licensePlate` [Opcional], `vehicleDescription` [Opcional], `scheduledDate`, `estimatedArrivalTime`).
* **Salida Esperada:** Objeto `InvitationResponse` que contiene el `invitationId` y el *payload*/imagen del `AccessToken` generado.
* **Eventos Disparados:** `InvitationGeneratedEvent` (Capturado por el adaptador de notificaciones para enviar el WhatsApp/Email al visitante con su código y para actualizar la interfaz del residente).
* **Reglas de Negocio (Invariantes):**
  * *RN-01.1 - Estado Operativo de la Casa:* La `House` destino debe tener obligatoriamente un `OperationalStatus` de `ACTIVE`. Si la casa está suspendida (ej. por morosidad o sanción administrativa), el sistema rechaza la creación y muestra un error al residente indicando que debe regularizar su situación.
  * *RN-01.2 - Propiedad y Autorización:* El `residentId` que invoca el comando debe estar explícitamente vinculado a la `houseId` para la cual se genera la visita. Un residente del Lote 10 no puede generar invitaciones para el Lote 11.
  * *RN-01.3 - Límite de Cuota de Visitas (Abuso):* El sistema validará que la `House` no exceda su límite máximo permitido de invitaciones activas para un mismo día (ej. para evitar que un residente organice un evento masivo sin pagar la cuota de la casa club).
  * *RN-01.4 - Validación Temporal:* El `scheduledDate` no puede ser una fecha en el pasado. Solo se permiten fechas actuales o futuras (hasta un límite de planeación, ej. 30 días).
  * *RN-01.5 - Validación de Suscripción Global (Anti-Blackout)*: Antes de validar el estado de la casa (`OperationalStatus`), el sistema verificará el estado operativo global de la Community. Si el fraccionamiento se encuentra bajo un CommercialBlackout (Servicio Suspendido), el backend rechazará el comando inmediatamente con un error de dominio (COMMUNITY_SERVICE_SUSPENDED), impidiendo la generación del token y devolviendo el mensaje amable para la UI del residente.


* **Consideraciones / Edge Cases (Técnicas):**
  * *Firma Criptográfica (Habilitador Offline):* El `AccessToken` (específicamente el *payload* del QR) debe generarse utilizando un estándar como JWT (JSON Web Token) y estar firmado con la llave privada del servidor (ej. usando HMAC o RSA). Esto es lo que permite que el **UC-ACC-11 (Validar Acceso Offline)** valide la autenticidad del código matemáticamente sin conexión a internet.
  * *Idempotencia en Creación:* Si el residente viaja en el metro, presiona "Generar Invitación", la red parpadea y lo presiona de nuevo, el comando debe usar una llave de idempotencia (o validar duplicidad exacta de `guestName`, `houseId` y `scheduledDate` en un margen de 5 minutos) para no generarle dos QRs distintos a la misma visita por error de red.


### UC-ACC-02: Cancelar Invitación (`CancelInvitationUseCase`)

* **Actor:** `PrimaryResident` o `SecondaryResident`.
* **Descripción:** Invalida un `AccessToken` existente antes de que sea utilizado. Cambia el estado de la `Invitation` de `PENDING` a `CANCELED`.
* **Comando de Entrada:** `CancelInvitationCommand` (`invitationId`, `residentId`).
* **Salida Esperada:** Confirmación de éxito o Error de Dominio (ej. `InvitationAlreadyInUseException`).
* **Eventos Disparados:** `InvitationCanceledEvent` (Notifica al visitante que su código fue revocado y actualiza la UI de la app del residente).
* **Reglas de Negocio (Invariantes):**
  * *RN-02.1 - Bloqueo por Estancia Activa:* El sistema **rechazará** la cancelación si el estado actual de la `Invitation` es `IN_USE` (el visitante ya ingresó y no ha salido). El ciclo debe cerrarse forzosamente mediante una salida física o una intervención del `CommunityAdmin`.
  * *RN-02.2 - Propiedad del Dato:* Un `SecondaryResident` solo puede cancelar las invitaciones que él mismo creó. El `PrimaryResident` puede cancelar las suyas y las de sus residentes secundarios.


* **Consideraciones / Edge Cases:**
  * *Brecha de Latencia Offline:* Existe un riesgo operativo aceptado. Si el residente cancela la invitación en el backend (nube) justo cuando la `KioskApp` en caseta se quedó sin internet, la `KioskApp` dejará entrar al visitante basándose en su caché local desactualizado. Al regresar el internet, el sistema detectará la anomalía, procesará la entrada y dejará una alerta en el `AuditLog`.


### UC-ACC-14: Actualizar Contacto de Invitación (`UpdateInvitationContactUseCase`)

* **Actor:** Residente (A través de la aplicación móvil).
* **Descripción:** Flujo que permite a un residente modificar el canal o la dirección de contacto (correo electrónico o número de WhatsApp) de una invitación previamente generada. Este caso de uso es responsable únicamente de validar y persistir la mutación del dato en la base de datos de accesos, emitiendo un evento de dominio para que otros contextos (como Notificaciones) reaccionen si es necesario.
* **Comando de Entrada:** `UpdateInvitationContactCommand`
  * `invitationId` (ID único de la invitación a modificar).
  * `residentId` (ID del residente que ejecuta la acción, para autorización).
  * `newContactMethod` (El nuevo canal: `EMAIL` o `WHATSAPP`).
  * `newContactAddress` (La nueva dirección de correo o número telefónico).
* **Salida Esperada:** Confirmación de actualización exitosa de la entidad `Invitación`.
* **Eventos Disparados:** `InvitationContactUpdatedEvent`.

* **Reglas de Negocio (Invariantes):**
  * *RN-ACC-14.1 - Autorización de Propiedad:* El sistema validará estrictamente que el `residentId` del comando coincida con el creador original de la invitación (o pertenezca a la misma unidad privativa con permisos de edición), evitando que un vecino modifique las invitaciones de otro.
  * *RN-ACC-14.2 - Validación de Estado (Vigencia):* Solo se permite modificar el contacto si la invitación se encuentra en estado `PENDING`. Si la invitación ya transicionó a `IN_USE`, `COMPLETED`, `CANCELED` o `EXPIRED`, la mutación se rechaza inmediatamente lanzando una excepción de dominio.
  * *RN-ACC-14.3 - Sanitización de Entrada:* El sistema debe validar que el `newContactAddress` cumpla con el formato correcto según el canal elegido (Regex para correo electrónico, o formato E.164 para números de WhatsApp).
  * *RN-ACC-14.4 - Idempotencia:* Si el residente envía el comando con exactamente los mismos datos de contacto que ya tiene la invitación, el sistema responderá con éxito pero omitirá la escritura en base de datos y no disparará el evento de actualización.

* **Consideraciones / Edge Cases:**
  * *Orquestación del Reenvío:* Este caso de uso **no envía mensajes**. Al finalizar exitosamente, dispara el `InvitationContactUpdatedEvent`. El frontend del residente puede aprovechar este éxito para habilitar el botón de "Reenviar", o bien, el sistema podría estar configurado para que el Contexto de Notificaciones escuche este evento y dispare automáticamente el `UC-NOT-06` (Reenviar Pase).


### UC-ACC-15: Solicitar Reenvío de Invitación (`RequestInvitationResendUseCase`)

* **Actor:** Residente (A través de la aplicación móvil).
* **Descripción:** Flujo que permite a un residente accionar explícitamente el reenvío de un pase de acceso a su visitante. Este caso de uso asume que los datos de contacto en la base de datos son correctos. Recupera la invitación, valida vigencia y permisos, empaqueta los datos actuales y dispara el evento de dominio para que Notificaciones ejecute el envío físico.
* **Comando de Entrada:** `RequestInvitationResendCommand`
  * `invitationId` (ID único de la invitación).
  * `residentId` (ID del residente que ejecuta la acción, para autorización).
* **Salida Esperada:** Confirmación de solicitud de reenvío aceptada (HTTP 202 Accepted).
* **Eventos Disparados:** `InvitationResendRequestedEvent` (Viaja con el *payload* completo recuperado de la base de datos: QR, NIP, nombres, método de contacto y dirección).

* **Reglas de Negocio (Invariantes):**
  * *RN-ACC-15.1 - Autorización de Propiedad:* El sistema validará estrictamente que el `residentId` pertenezca a la misma unidad privativa que generó la invitación original.
  * *RN-ACC-15.2 - Validación de Vigencia:* La solicitud será rechazada inmediatamente con una excepción de dominio si la invitación se encuentra en cualquier estado distinto a `PENDING` (ej. `IN_USE`, `COMPLETED`, `CANCELED` o `EXPIRED`).
  * *RN-ACC-15.3 - Control de Abuso (Rate Limiting de Dominio):* Si el residente ha solicitado el reenvío de esta misma invitación más de 3 veces en la última hora, la solicitud será denegada temporalmente.

* **Consideraciones / Edge Cases:**
  * *Orquestación de Cambios de Contacto:* Si el residente necesita enviar el pase a un número distinto al registrado originalmente, el cliente (Frontend) debe ejecutar primero el `UC-ACC-14` (Actualizar Contacto) y, tras su éxito, invocar este caso de uso. El `UC-ACC-15` es estrictamente de solo lectura sobre los datos de la invitación.


### UC-ACC-16: Consultar Mis Invitaciones Activas (`ListActiveInvitationsQuery`)

* **Actor:** `PrimaryResident` o `SecondaryResident` (operando desde la App Móvil o Web).
* **Descripción:** Flujo de lectura (Query) que alimenta la pantalla principal del residente. El sistema recupera un listado ligero y paginado de todos los pases de acceso vigentes o en curso asociados a su unidad privativa. Al ser el *endpoint* más consultado de la aplicación, está altamente optimizado para no saturar la base de datos ni el ancho de banda del celular.
* **Consulta de Entrada:** `GetActiveInvitationsQuery` (`residentId`, `limit` [ej. 20], `offset`).
* **Salida Esperada:** `PaginatedList<InvitationSummaryDTO>`. Cada DTO contiene datos ligeros: `invitationId`, `guestName`, `visitType`, `scheduledDate`, `estimatedArrivalTime` y el `status` actual.
* **Eventos Disparados:** Ninguno. (Por ser una operación de solo lectura, no muta el estado ni genera eventos de dominio).

* **Reglas de Consulta (Filtros y Autorización):**
  * *RQ-16.1 - Aislamiento de Propiedad (Tenant Isolation):* El sistema resolverá la `House` a la que está vinculado el `residentId` y filtrará la base de datos para devolver **exclusivamente** las invitaciones generadas para esa casa. Un residente no puede consultar el tráfico de sus vecinos.
  * *RQ-16.2 - Definición de "Activo" (Filtro de Estado):* La consulta aplicará un filtro estricto a nivel de base de datos para devolver únicamente los registros cuyo estado sea `PENDING` (visita programada) o `IN_USE` (visita actualmente dentro del fraccionamiento). Los estados `COMPLETED`, `CANCELED` y `EXPIRED` se excluyen de esta vista.
  * *RQ-16.3 - Ordenamiento Cronológico (UX):* Para dar la mejor experiencia, la lista se ordenará de forma ascendente por `scheduledDate` y `estimatedArrivalTime`, mostrando hasta arriba al visitante que está próximo a llegar o que ya está adentro.
  * *RQ-16.4 - Omisión de Carga Pesada (Lazy Loading):* Para garantizar un tiempo de respuesta menor a 200ms, este caso de uso **no devolverá** el `AccessToken` firmado (el payload del QR) ni el NIP numérico. Esos datos pesados se descargarán solo cuando el usuario decida ver el detalle.

* **Consideraciones / Edge Cases (Técnicas):**
  * *Uso de Índices:* El equipo de base de datos debe asegurar que exista un índice compuesto en PostgreSQL (ej. `house_id, status, scheduled_date`) para que esta consulta no haga escaneos secuenciales completos (*Full Table Scans*), ya que se ejecutará miles de veces al día.

### UC-ACC-17: Consultar Detalle de Invitación (`GetInvitationDetailQuery`)

* **Actor:** `PrimaryResident` o `SecondaryResident` (operando desde la App Móvil o Web).
* **Descripción:** Flujo de lectura profunda (Query). Cuando el residente selecciona una invitación específica, este caso de uso recupera la información íntegra de la visita para propósitos de auditoría personal y gestión. Por diseño de seguridad, **este caso de uso jamás extrae ni devuelve los secretos de acceso** (Código QR o NIP), ya que el residente actúa como autorizador y no como portador del pase.
* **Consulta de Entrada:** `GetInvitationDetailQuery` (`invitationId`, `residentId`).
* **Salida Esperada:** Objeto `InvitationDetailDTO`. Contiene todos los atributos administrativos de la invitación: `guestName`, `visitType`, `licensePlate`, `vehicleDescription`, método de contacto (`EMAIL`/`WHATSAPP`), dirección de destino, y un sub-objeto con los *timestamps* del ciclo de vida (creación, hora real de entrada, hora real de salida) y el estado actual (`status`).
* **Eventos Disparados:** Ninguno. (Operación de solo lectura).

* **Reglas de Consulta (Filtros y Autorización):**
  * *RQ-17.1 - Autorización Estricta de Propiedad (IDOR Protection):* El sistema debe validar obligatoriamente que el `residentId` esté asociado a la misma `House` a la que pertenece la `invitationId` solicitada. Si hay un desajuste, el sistema rechazará la consulta con un `HTTP 403 Forbidden`.
  * *RQ-17.2 - Disponibilidad Histórica Continua:* El detalle de una invitación puede ser consultado sin importar su estado actual (`PENDING`, `IN_USE`, `COMPLETED`, `CANCELED`, `EXPIRED`). El residente mantiene el derecho de auditar el ciclo de vida de la visita en todo momento.
  * *RQ-17.3 - Ceguera Criptográfica (Zero-Trust al Cliente):* El backend excluirá explícitamente los campos `tokenPayload` (el JWT del QR) y `numericCode` (NIP) de la consulta a la base de datos y de la serialización del DTO. Estos secretos solo pueden ser manipulados por el motor de generación (`UC-ACC-01`), el validador de caseta (`UC-ACC-03`) y el orquestador de reenvío (`UC-ACC-15`).

* **Consideraciones / Edge Cases (Técnicas):**
  * *Experiencia de Usuario (UI):* Al no recibir la imagen del QR, la pantalla de "Detalle" en la aplicación móvil del residente debe enfocarse en el estatus (ej. un mapa de progreso: "Generada -> Enviada a WhatsApp -> En Curso -> Finalizada") y en habilitar los botones de acción (`Reenviar` o `Cancelar`), en lugar de mostrar un código visual.


### UC-ACC-18: Consultar Historial de Invitaciones (`ListInvitationHistoryQuery`)

* **Actor:** `PrimaryResident` o `SecondaryResident` (operando desde la App Móvil o Web).
* **Descripción:** Flujo de lectura paginada (Query). Permite al residente auditar las visitas pasadas, expiradas o revocadas de su unidad privativa. Es una consulta de baja frecuencia en comparación con el listado de activas, utilizada principalmente para aclaraciones (ej. "¿A qué hora se fue el plomero ayer?").
* **Consulta de Entrada:** `GetInvitationHistoryQuery`
  * `residentId` (ID del residente).
  * `limit` (Paginación: ej. 30 registros).
  * `offset` (Paginación: índice de inicio).
  * `filterByStatus` (Opcional: Para buscar solo `CANCELED`, `COMPLETED` o `EXPIRED`).
* **Salida Esperada:** `PaginatedList<InvitationSummaryDTO>` con la información básica de las invitaciones históricas ordenadas de forma descendente.
* **Eventos Disparados:** Ninguno.

* **Reglas de Consulta (Filtros y Autorización):**
  * *RQ-18.1 - Aislamiento de Propiedad:* Al igual que en las consultas activas, se filtra estrictamente para devolver solo los registros asociados a la `House` del `residentId`.
  * *RQ-18.2 - Exclusión de Activas:* La consulta omitirá cualquier invitación cuyo estado actual sea `PENDING` o `IN_USE`, ya que esas pertenecen exclusivamente al Dashboard principal (`UC-ACC-16`).
  * *RQ-18.3 - Ordenamiento Cronológico Inverso:* Los resultados deben devolverse ordenados de más recientes a más antiguos (`ORDER BY scheduledDate DESC`), mostrando primero las visitas que acaban de terminar o caducar.


## 2. Validación y Operación en Caseta (Kiosk App & Hardware)

### UC-ACC-03: Registrar Entrada Online (`RegisterOnlineEntryUseCase`)

* **Actor:** `KioskApp` (Hardware en carril de entrada).
* **Descripción:** Flujo nominal de entrada con conexión a internet. El sistema decodifica el `tokenPayload` para identificar la `Invitation` y su `House` destino. Valida secuencialmente que la invitación esté en estado `PENDING` para el día de hoy y consulta el `OperationalStatus` para confirmar que la casa no esté suspendida. Posteriormente, verifica el `visitType` para determinar si requiere evaluar la `ParkingQuota`. Al aprobarse, actualiza el estado de la invitación a `IN_USE`, decrementa en 1 el contador de aforo (solo si el tipo de visita lo amerita) y devuelve la señal de apertura.
* **Comando de Entrada:** `RegisterOnlineEntryCommand` (`tokenPayload`, `kioskId`).
* **Salida Esperada:** Objeto `ValidationResult` (status: `GRANTED` / `DENIED`, `reason`).
* **Eventos Disparados:** 
  * `AccessGrantedEvent` (La `KioskApp` lo recibe para activar el relevador de la pluma; el backend dispara la notificación Push de "Tu visita ha llegado" al residente).
  * `AccessDeniedEvent` (Se registra el intento fallido en el `AuditLog` por motivos de seguridad).


* **Reglas de Negocio (Invariantes):**
  * *RN-03.1 - Validez Temporal:* La fecha actual del sistema debe coincidir con el `scheduledDate` de la `Invitation`.
  * *RN-03.2 - Prevención de Fraude (Anti-passback):* El estado actual de la `Invitation` debe ser obligatoriamente `PENDING`. Si el estado es `IN_USE` (ya ingresó) o `CANCELED`, se rechaza el acceso.
  * *RN-03.3 - Límite de Aforo Condicionado (`ParkingQuota`):* La validación y descuento del aforo dependerá estrictamente del `visitType` de la invitación. Si es `VEHICLE` y el aforo está lleno (`ParkingQuota` disponible = 0), el acceso automatizado se deniega por "Estacionamiento Lleno", delegando la resolución al guardia. Si es `RIDE_HAILING`, `DELIVERY` o `PEDESTRIAN`, el sistema ignorará la restricción de aforo y no decrementará el contador al abrir la pluma.
  * *RN-03.4 - Estado Operativo de la Casa:* La `House` asociada debe tener un `OperationalStatus` igual a `ACTIVE`.
  * *RN-03.5 - Estado Comercial del Fraccionamiento:* La `Community` no debe estar bajo un `CommercialBlackout` (falta de pago de la suscripción).


* **Consideraciones / Edge Cases (Técnicas):**
  * *Condición de Carrera (Double Spend):* El backend (PostgreSQL) debe manejar bloqueos transaccionales (ej. *Pessimistic Locking* en la fila de la invitación) al leer y cambiar el estado a `IN_USE` para evitar que dos vehículos entren simultáneamente usando copias del mismo código QR en distintos carriles.
  * *Timeout de Red:* Si la llamada REST/HTTPS tarda más de 3 segundos, la `KioskApp` abortará la petición de red y ejecutará su caso de uso local (`RegisterOfflineEntryUseCase`).


### UC-ACC-04: Sincronizar Caché Offline (`SyncOfflineCacheUseCase`)

* **Actor:** `KioskApp` (Proceso en segundo plano / Tarea programada).
* **Descripción:** Proceso de red recurrente que garantiza la autonomía de la caseta. La `KioskApp` solicita al backend el estado actual de los accesos del día. El backend ejecuta una consulta cruzada que recupera todas las `Invitations` de la `Community` solicitada donde el `scheduledDate` sea igual a la fecha actual, excluyendo únicamente las que tengan estado `EXPIRED`. Críticamente, el backend cruza esta información con el `Community Context` para **adjuntar el `OperationalStatus`** a cada registro en lugar de filtrarlos. El resultado se serializa y se devuelve a la `KioskApp` para que reemplace su estado local, permitiéndole dar mensajes de rechazo precisos durante una contingencia.
* **Consulta de Entrada:** `GetValidTokensForTodayQuery` (`communityId`, `currentDate`).
* **Salida Esperada:** `List<OfflineTokenDTO>` (Atributos: `invitationId`, `tokenPayload`, `invitationStatus` [PENDING / IN_USE / CANCELED], `operationalStatus` [ACTIVE / SUSPENDED], `visitType`).
* **Eventos Disparados:** Ninguno a nivel de dominio central (Operación de solo lectura). La `KioskApp` puede disparar un evento interno técnico (ej. `LocalCacheUpdatedEvent`).
* **Reglas de Negocio (Invariantes):**
  * *RN-04.1 - Minimización de Carga Temporal:* La consulta en la base de datos central debe acotarse estrictamente a la fecha actual (`currentDate`).
  * *RN-04.2 - Propagación del Estado Operativo (Visibilidad de Suspensión):* Si una `House` es suspendida por el administrador (por morosidad o sanción), la sincronización **debe incluir** las invitaciones de esa casa, pero mapeando explícitamente el atributo `operationalStatus` como `SUSPENDED`. Esto habilita al caso de uso offline (`RegisterOfflineEntryUseCase`) a rechazar el acceso mostrando el motivo exacto al guardia.
  * *RN-04.3 - Propagación de Cancelaciones:* El backend debe incluir en la lista las invitaciones con estado `CANCELED`, para obligar a la `KioskApp` a invalidar esos QRs localmente y poder mostrar el mensaje "Invitación Cancelada por el Residente".
  * *RN-04.4 - Seguimiento del Anti-passback:* Las invitaciones con estado `IN_USE` deben incluirse en la descarga para indicar a la `KioskApp` que ese código ya fue utilizado hoy, previniendo dobles ingresos si se cae la red.


* **Consideraciones / Edge Cases (Técnicas):**
  * *Atomicidad de la Actualización Local:* Al recibir la `List<OfflineTokenDTO>`, la `KioskApp` debe actualizar su almacenamiento temporal dentro de una sola transacción. Si la tablet crashea a mitad de la escritura, el caché no debe quedar corrupto; debe revertir al estado anterior.
  * *Frecuencia vs Frescura (Polling Strategy):* La `KioskApp` ejecutará este caso de uso cada 5 minutos de forma automática.



### UC-ACC-05: Procesar Eventos Diferidos (`ProcessDeferredEventsUseCase`)

* **Actor:** `KioskApp` (Proceso automático al recuperar conexión de red).
* **Descripción:** Flujo de reconciliación asíncrona. La `KioskApp` envía en bloque (batch) todos los eventos de acceso y salida que autorizó localmente durante una contingencia de red. El backend recibe este lote, ordena los eventos cronológicamente por su timestamp real de lectura, y los procesa uno por uno. Actualiza los estados de las `Invitations` (a `IN_USE` o `COMPLETED`), registra los movimientos en el `AuditLog` y recalcula la `ParkingQuota` del fraccionamiento (evaluando el `visitType` de cada evento para no alterar el aforo incorrectamente). Si detecta conflictos lógicos (ej. una cancelación en la nube que ocurrió al mismo tiempo que un ingreso offline), el sistema prioriza el evento físico y genera una alerta de auditoría.
* **Comando de Entrada:** `SyncDeferredAccessesCommand` (Lista de `DeferredAccessDTO`: `invitationId`, `type` [ENTRY / EXIT], `exactTimestamp`, `uniqueEventId` [UUID generado en la tablet]).
* **Salida Esperada:** Objeto `SyncResult` (Lista de IDs procesados con éxito y lista de fallos, para que la tablet los borre de su memoria local).
* **Eventos Disparados:** 
  * `DeferredAccessesProcessedEvent` (Ajusta el aforo global del fraccionamiento).
  * Versiones diferidas de `AccessGrantedEvent` o `DepartureRegisteredEvent` para notificar llegadas o salidas en atraso.
  * `OfflineOverrideAlertEvent` (Condicional: Se dispara únicamente si el sistema detecta que un visitante ingresó físicamente usando un código que ya había sido cancelado en la nube. Detona una comunicación de transparencia hacia el residente).
* **Reglas de Negocio (Invariantes):**
  * *RN-05.1 - Idempotencia Estricta:* El backend **no debe** procesar el mismo acceso dos veces. Si la red parpadea y la tablet envía el mismo lote dos veces, el backend debe usar el `uniqueEventId` de cada evento para ignorar los que ya fueron guardados, evitando restar el doble de lugares de estacionamiento.
  * *RN-05.2 - Ordenamiento Cronológico:* El lote entero debe ordenarse por `exactTimestamp` de más antiguo a más reciente antes de procesarse. Procesar una salida diferida antes de su entrada correspondiente corrompería el estado de la `Invitation`.
  * *RN-05.3 - Resolución de Conflictos (La realidad física gana y UX Alert):* Si el residente canceló la invitación (`CANCELED`) en la nube, pero el visitante ingresó offline (`ENTRY`) en la caseta debido a un desfase de red, el backend **debe aceptar el ingreso**, cambiar el estado a `IN_USE` y levantar una alerta en el `AuditLog` ("Ingreso físico posterior a cancelación online").
    * *Mitigación de UX:* Para evitar que el residente perciba una falla de seguridad, el sistema disparará inmediatamente el `OfflineOverrideAlertEvent`. Esto enviará una notificación Push explicativa y transparente: *"Aviso: Tu visita [Nombre] logró ingresar debido a una intermitencia de red en caseta, justo antes de que tu cancelación hiciera efecto."*
  * *RN-05.4 - Tolerancia de Aforo Negativo:* Al procesar las entradas diferidas, la `ParkingQuota` debe actualizarse. Si durante el periodo offline entraron más coches de los permitidos, el contador de aforo disponible puede temporalmente caer por debajo de cero (ej. -2). El backend debe permitir esto para reflejar la realidad física, pero disparará una alerta crítica al `CommunityAdmin`.
  * *RN-05.5 - Resolución de Eventos Huérfanos:* Si el backend recibe un evento diferido de tipo `ENTRY` para una `Invitation` que ya se encuentra en estado `COMPLETED` (debido a la RN-08.1), el backend **no lanzará error ni cambiará el estado** a `IN_USE`. Simplemente aceptará el evento, actualizará el `entryTimestamp` real en la base de datos para corregir las estadísticas de duración de la visita, y resolverá la advertencia previa en el `AuditLog`.
  * *RN-05.6 - Recálculo Condicionado de Aforo:* Al procesar lotes de entradas y salidas, el sistema evaluará estrictamente el `visitType`. Las entradas y salidas de tipos transitorios (`RIDE_HAILING`, `DELIVERY`, `PEDESTRIAN`) mutarán los estados de la invitación pero tendrán un impacto neto de cero (0) sobre la `ParkingQuota`.


* **Consideraciones / Edge Cases (Técnicas):**
  * *Rendimiento en Bloque (Batch Processing):* Si la caseta estuvo sin internet 6 horas, el lote podría tener cientos de registros. El backend debe procesar la lista usando operaciones en lote (`Batch Updates` en PostgreSQL) para evitar el problema de "N+1 consultas" y no saturar el pool de conexiones de la base de datos.
  * *Parcialidad de Sincronización:* Si el lote tiene 100 registros y el número 50 causa una excepción de base de datos impredecible, los primeros 49 deben guardarse con éxito. El backend responde qué UUIDs se procesaron, y la tablet solo reintentará los fallidos más tarde.



## 3. Excepciones y Contingencias (Guard Console Flow)

### UC-ACC-06: Registrar Llegada Sorpresa (`RegisterUnscheduledVisitUseCase`)

* **Actor:** `SecurityGuard` (operando desde la `GuardConsole` en caseta).
* **Descripción:** Flujo manual para gestionar visitantes sin `AccessToken`. El guardia identifica la `House` destino y captura los datos básicos del visitante. El sistema valida en tiempo real si la casa tiene permitido recibir visitas (estado activo y aforo disponible). Si las reglas de negocio lo permiten, el sistema genera una `Invitation` instantánea en estado `IN_USE`, decrementa la `ParkingQuota` y envía un comando de apertura a la barrera. Simultáneamente, alerta al residente sobre la llegada.
* **Comando de Entrada:** `RegisterUnscheduledVisitCommand` (`houseId`, `guestName`, `visitType` [PEDESTRIAN, VEHICLE, RIDE_HAILING, DELIVERY], `licensePlate` [Opcional], `vehicleDescription` [Opcional], `guardId`, `reason` [String libre, ej. "Entrega Amazon"]).
* **Salida Esperada:** Objeto `ValidationResult` (status: `GRANTED` / `DENIED`, `reason`).
* **Eventos Disparados:** 
  * `ManualAccessGrantedEvent` (La `GuardConsole` lo envía a la `KioskApp` vía red local o API para abrir la barrera física).
  * `UnscheduledVisitAlertEvent` (Dispara una notificación Push urgente y/o un correo al `PrimaryResident` y `SecondaryResidents` indicando: *"Visita sorpresa registrada en caseta"*).
  * `ParkingSpaceReservedEvent` (Actualiza el contador global de aforo, condicionado a que el `visitType` consuma espacio según la RN-06.2).


* **Reglas de Negocio (Invariantes):**
  * *RN-06.1 - Estado Operativo de la Casa:* Al igual que en una entrada con código, la `House` debe tener un `OperationalStatus` igual a `ACTIVE`. Si la casa está `SUSPENDED` (por morosidad o sanción administrativa), la `GuardConsole` debe denegar el registro inmediatamente mostrando el mensaje al guardia, impidiendo que el guardia haga favores y abra la pluma.
  * *RN-06.2 - Límite de Aforo (`ParkingQuota`) y Tipos de Visita Transitorios:* El sistema evaluará la disponibilidad de estacionamiento basándose estrictamente en el `visitType`:
    * Si el `visitType` es **`VEHICLE`** (visitante regular) y el aforo está lleno (`ParkingQuota` = 0), el sistema **rechazará** el ingreso, pues no hay dónde estacionarlo.
    * Si el `visitType` es **`RIDE_HAILING`** (Uber/Taxi), **`DELIVERY`** (Paquetería/Comida) o **`PEDESTRIAN`** (Peatón), el sistema **ignorará la restricción de aforo** y permitirá el ingreso, ya que no consumen cajones de estacionamiento.
  * *RN-06.3 - Auditoría Obligatoria:* Todo registro manual debe quedar irremediablemente atado al `guardId` que estaba en turno operando la consola, guardándose en el `AuditLog` para evitar abusos o colusión.
  * *RN-06.4 - Monitoreo de Tránsito Efímero:* Aunque los `RIDE_HAILING` y `DELIVERY` no consumen aforo, el sistema registrará su entrada. Si no registran su salida en un tiempo prudente (ej. 15 minutos), el sistema detonará una alerta visual en la `GuardConsole` indicando: *"Vehículo de tránsito excedió el tiempo límite dentro del fraccionamiento"*.

* **Consideraciones / Edge Cases (Técnicas):**
  * *Tolerancia a Red (LAN local):* Si el fraccionamiento se queda sin internet general, pero la red interna (LAN) de la caseta funciona, la `GuardConsole` no podrá validar el `OperationalStatus` contra el backend en la nube. En este escenario degradado, el sistema debería apoyarse en el último caché descargado por la `KioskApp` (ver comunicación LAN en Nivel 2 C4) para validar a nivel local y registrar el evento para sincronización diferida.
  * *Tipos de Visita Especiales (`DELIVERY`):* Los repartidores (paquetería, comida) suelen requerir un tratamiento distinto. El negocio podría requerir que un `DELIVERY` tenga un tiempo máximo de estancia (ej. 15 minutos) antes de detonar una alerta en la consola del guardia.


### UC-ACC-07: Ejecutar Apertura de Contingencia (`ExecuteEmergencyOverrideUseCase`)

* **Actor:** `SecurityGuard` (operando desde la `GuardConsole`).
* **Descripción:** Flujo crítico de evasión de reglas lógicas (Bypass) diseñado para priorizar la velocidad de respuesta y salvaguardar la vida humana o la integridad operativa. Permite al guardia enviar un comando directo de apertura a la barrera física (Entrada, Salida o Ambas simultáneamente). **El botón de emergencia siempre está accesible y es de acción inmediata (Un solo clic).** Al presionarlo, la(s) pluma(s) se abre(n) al instante. En lugar de bloquear la operación de la caseta, el sistema añade este evento a una **"Cola de Justificaciones Pendientes"** (una alerta visual persistente en la UI). El guardia puede seguir operando normalmente y abrir a otros vehículos durante la crisis. Las justificaciones detalladas se exigen una vez que la contingencia termina.
* **Comandos de Entrada (Flujo Desacoplado):** 
  1. `TriggerEmergencyOverrideCommand` (`guardId`, `gateDirection` [ENTRY, EXIT, BOTH]). -> *Abre la barrera inmediatamente y encola la tarea.*
  2. *(Ver UC-ACC-12 para el paso de resolución administrativa).*
* **Salida Esperada:** Apertura electromecánica inmediata de la barrera física especificada en el primer comando.
* **Eventos Disparados:** 
  * `EmergencyOverrideTriggeredEvent` (Abre la pluma, guarda el registro inicial y levanta una alerta temporal en el dashboard del `CommunityAdmin`: *"Apertura de emergencia en progreso"*).
* **Reglas de Negocio (Invariantes):**
  * *RN-07.1 - Evasión Absoluta (Bypass total):* El comando de disparo (`Trigger`) ignora por diseño la `ParkingQuota`, el `OperationalStatus`, el estado de las invitaciones y cualquier otra restricción lógica del sistema. Su única función es abrir la pluma en milisegundos y registrar la acción.
  * *RN-07.2 - Cierre de Turno Condicionado:* Aunque la consola no se bloquea operativamente durante la emergencia, el sistema **no permitirá** que el `SecurityGuard` cierre su sesión (Logout) ni entregue el turno al siguiente guardia si existen eventos en la "Cola de Justificaciones Pendientes". El cumplimiento de la auditoría se fuerza al final de la jornada.
  * *RN-07.3 - Cierre de Incidente por Omisión (Auditoría Estricta):* Si el periodo de gracia expira sin que el `SecurityGuard` capture la justificación requerida, el sistema ejecutará un cierre automatizado del evento. Cambiará el estado del rastro de contingencia de `PENDING_JUSTIFICATION` a `UNJUSTIFIED_SECURITY_INCIDENT` en el `AuditLog`.
    * *Aclaración de Alcance:* El sistema no gestiona expedientes laborales, sanciones ni métricas de RH. Su única responsabilidad es clasificar este evento como una anomalía operativa inmutable, la cual se resaltará en rojo (o como alerta de seguridad) en los reportes del panel de control del administrador del fraccionamiento (`CommunityAdmin`), delegando a este último cualquier acción disciplinaria por fuera de la plataforma.


* **Consideraciones / Edge Cases (Técnicas):**
  * *Ejecución Offline (LAN Bypass):* Si la `GuardConsole` pierde conexión con el backend en la nube, el comando `TriggerEmergencyOverrideCommand` debe viajar obligatoriamente por la red local (LAN) directamente hacia la `KioskApp` (o controlador de hardware) para accionar el relé. El evento de auditoría inicial se guardará en la base de datos local de la consola y se sincronizará cuando regrese el internet.
  * *Persistencia de la Cola de Tareas:* La "Cola de Justificaciones Pendientes" debe persistir localmente. Si la caseta pierde energía o la computadora se reinicia durante la emergencia, al volver a abrir la aplicación de la consola, las justificaciones pendientes deben seguir visibles y exigibles.
  * *Falla Total de Energía/Red Local:* Si la red LAN también está caída o no hay suministro eléctrico (y no hay UPS/Batería de respaldo), el software queda inoperante. La caseta debe contar con un botón físico electromecánico (cableado directo al motor de la barrera) que corte la energía del brazo o lo levante mecánicamente. Ese evento físico quedaría fuera del alcance del registro de AxolPass.


### UC-ACC-20: Registrar Salida Manual desde Consola (`RegisterManualDepartureUseCase`)

* **Actor:** `SecurityGuard` (operando desde la `GuardConsole` en caseta).
* **Descripción:** Flujo manual de evacuación. Permite al guardia registrar la salida de visitantes que no cuentan con un código QR para escanear en la pluma (típicamente visitantes registrados mediante el UC-ACC-06: Llegada Sorpresa). El guardia selecciona el vehículo desde una lista de "Visitas Activas" en su pantalla. El sistema valida el estado, cierra el ciclo a `COMPLETED`, libera el aforo correspondiente y abre la barrera física de salida.
* **Comando de Entrada:** `RegisterManualDepartureCommand` (`invitationId`, `guardId`).
* **Salida Esperada:** Apertura de la barrera de salida y remoción visual de la visita en la lista de activos.
* **Eventos Disparados:** 
  * `DepartureRegisteredEvent` (Notifica al residente sobre la salida).
  * `ParkingSpaceReleasedEvent` (Suma +1 al aforo, condicionado a que el `visitType` original haya consumido espacio).
  * `ManualAccessGrantedEvent` (Comando a nivel de hardware para levantar la pluma de salida).
* **Reglas de Negocio (Invariantes):**
  * *RN-20.1 - Restitución Condicionada de Aforo:* Al igual que en una salida automatizada, el sistema verificará el `visitType` de la invitación seleccionada. Solo sumará +1 al contador de aforo si el tipo de visita correspondía a un vehículo regular (`VEHICLE`). Si el guardia le da salida manual a un `DELIVERY` (repartidor) o `PEDESTRIAN`, el aforo permanecerá intacto para evitar "creaciones mágicas" de estacionamiento.
  * *RN-20.2 - Auditoría de Acción Manual:* A diferencia del cierre automático (UC-ACC-08), esta acción queda vinculada permanentemente al `guardId` en el `AuditLog`. Esto es crítico para prevenir que un guardia malintencionado libere lugares de estacionamiento de vehículos que aún están adentro para meter a otras personas.
  * *RN-20.3 - Restricción de Estado:* El comando fallará (y el UI mostrará un error) si el `invitationId` no se encuentra en estado `IN_USE`.

* **Consideraciones / Edge Cases (Técnicas):**
  * *Tolerancia a Red (Sincronización Diferida):* Si el fraccionamiento no tiene internet (pero la red LAN local funciona), la `GuardConsole` ejecutará este comando utilizando su base de datos local y abriendo la pluma a través del controlador de hardware. El evento de salida manual se encolará y se sincronizará hacia la nube cuando regrese la conexión, utilizando la misma infraestructura de reconciliación definida en el UC-ACC-05.
  * *Visualización para el Guardia:* Para que este caso de uso funcione operativamente, el frontend de la `GuardConsole` debe alimentarse de una consulta continua (ej. un Query paginado de invitaciones locales filtradas por estado `IN_USE` ordenadas por tiempo de permanencia).


### UC-ACC-12: Justificar Contingencia Pendiente (`ResolvePendingEmergencyUseCase`)

* **Actor:** `SecurityGuard` (operando desde la `GuardConsole`).
* **Descripción:** Flujo administrativo obligatorio que complementa una apertura de emergencia previa (UC-ACC-07). Permite al guardia sacar un evento de evasión de reglas de la "Cola de Justificaciones Pendientes" proporcionando la categorización y el contexto exacto de por qué se abrió la barrera de forma anómala. Al completarse este flujo con éxito, el evento de emergencia pasa de un estado temporal de alerta a un registro permanente y cerrado en el historial de auditoría del fraccionamiento.
* **Comando de Entrada:** `ResolveEmergencyOverrideCommand` (`overrideEventId`, `overrideType` [MEDICAL_EMERGENCY, POLICE, FIRE_DEPARTMENT, HARDWARE_FAILURE, OTHER], `mandatoryJustification` [Texto libre, ej. "Ambulancia Cruz Roja placas XYZ"]).
* **Salida Esperada:** Confirmación de auditoría guardada y remoción visual del evento en la "Cola de Justificaciones Pendientes" de la interfaz del guardia.
* **Eventos Disparados:** `EmergencyOverrideResolvedEvent` (Actualiza el estado del evento original en el `AuditLog` inmutable y, opcionalmente, dispara un reporte consolidado o alerta mitigada al `CommunityAdmin`).
* **Reglas de Negocio (Invariantes):**
  * *RN-12.1 - Justificación Estricta y Verificable:* El sistema rechazará el comando si el campo `mandatoryJustification` está vacío, contiene solo espacios, o tiene menos de 10 caracteres. El guardia debe proveer una explicación descriptiva y útil para una auditoría (ej. "Bomberos apagando fuego en lote 15").
  * *RN-12.2 - Inmutabilidad Post-Resolución:* Una vez que el evento cambia de estado pendiente a resuelto (es decir, una vez que este caso de uso se ejecuta con éxito para un `overrideEventId` específico), ni el `SecurityGuard` ni el `CommunityAdmin` pueden editar o borrar la justificación. El registro se vuelve prueba legal inalterable.
  * *RN-12.3 - Restricción de Autoría (Propiedad del Evento):* Idealmente, la justificación debe ser llenada por el mismo `guardId` que disparó la contingencia. Si un guardia de relevo intenta justificar un evento de un turno anterior, el sistema debe permitirlo (para limpiar la cola), pero el `AuditLog` registrará explícitamente una divergencia de autoría (ej. "Disparado por Guardia A, Justificado por Guardia B"), levantando una bandera amarilla para el administrador.


* **Consideraciones / Edge Cases (Técnicas):**
  * *Sincronización Diferida (Offline Mode):* Si el guardia llena la justificación mientras la `GuardConsole` no tiene conexión a internet (en la nube), el sistema debe guardar la resolución localmente, quitar la alerta visual de la pantalla, y encolar el `ResolveEmergencyOverrideCommand` para ser transmitido al backend en cuanto se restablezca la conexión, garantizando que el guardia no se quede "atrapado" sin poder cerrar su turno por falta de red.


## 4. Ciclo de Salida y Liberación de Recursos

### UC-ACC-08: Registrar Salida de Visitante (`RegisterDepartureUseCase`)

* **Actor:** `KioskApp` (Hardware en carril de salida) o `GuardConsole` (Escaneo manual).
* **Descripción:** Flujo nominal para cerrar el ciclo de vida de una visita. El sistema decodifica el `tokenPayload`. Verifica el estado actual de la `Invitation` para determinar si el visitante tiene un registro de entrada previo. Cambia el estado del acceso a `COMPLETED` y, si el tipo de visita lo amerita, libera un espacio en la `ParkingQuota` de la `Community` para que otro visitante pueda entrar. Si el sistema detecta anomalías lógicas (como un visitante intentando salir sin haber registrado entrada), prioriza la evacuación física, permite la salida y genera un registro de auditoría para conciliar los datos posteriormente.
* **Comando de Entrada:** `RegisterDepartureCommand` (`tokenPayload`, `kioskId`).
* **Salida Esperada:** Objeto `ValidationResult` (status: `GRANTED` / `DENIED`, `reason`).
* **Eventos Disparados:** 
  * `DepartureRegisteredEvent` (Notifica al `PrimaryResident` que su visita se ha retirado).
  * `ParkingSpaceReleasedEvent` (Incrementa en 1 la disponibilidad en el contador de aforo del fraccionamiento, **únicamente** si el `visitType` original consumía espacio).


* **Reglas de Negocio (Invariantes):**
  * *RN-08.1 - Flujo Nominal y Restitución Condicionada de Aforo:* Si el estado de la `Invitation` es `IN_USE`, el acceso de salida se concede inmediatamente y el estado cambia a `COMPLETED`. El sistema **solo sumará +1** al contador de aforo si el `visitType` de la invitación original consumía aforo (ej. `VEHICLE`). Si era `RIDE_HAILING`, `DELIVERY` o `PEDESTRIAN`, la salida solo muta el estado sin afectar la métrica, previniendo la "creación mágica" de estacionamientos.
  * *RN-08.2 - Tolerancia a Falla Parcial (El "Auto Fantasma"):* Si el estado de la `Invitation` es `PENDING` (el código nunca fue escaneado en la entrada, o la lectura falló), el sistema **asumirá una una evasión o desincronización** en la caseta de entrada. Para no bloquear el tráfico, el sistema concederá la salida y cambiará el estado de `PENDING` a `COMPLETED`. Sin embargo, estrictamente NO disparará el ParkingSpaceReleasedEvent (no sumará aforo), ya que ese acceso jamás fue descontado matemáticamente al ingresar. El sistema dejará una advertencia en el `AuditLog`: *"Salida registrada sin entrada previa. Aforo inalterado"*.
  * *RN-08.3 - Cruce de Medianoche (Tolerancia Temporal):* Si la `Invitation` tiene un `scheduledDate` del *día anterior*, pero su estado actual es `IN_USE` (el visitante entró a las 11:00 PM y está saliendo a las 2:00 AM), el sistema **no debe rechazar** la salida por "token expirado". Validará la salida correctamente y cerrará el ciclo.
  * *RN-08.4 - Prevención de Doble Salida:* Si el estado de la `Invitation` ya es `COMPLETED` o `CANCELED`, el sistema denegará la apertura de la pluma. Esto previene que alguien fotocopie el QR de salida para abrir la barrera fraudulentamente desde adentro.


* **Consideraciones / Edge Cases (Técnicas):**
  * *Salida Offline:* Si este caso de uso falla por *timeout* de red, la `KioskApp` abortará y delegará la ejecución al caso de uso local **UC-ACC-10 (`RegisterOfflineDepartureUseCase`)**, el cual abrirá la pluma inmediatamente registrando solo el timestamp local.
  * *Condición de Carrera en Aforo:* Al disparar el `ParkingSpaceReleasedEvent` (cuando aplique), la base de datos (PostgreSQL) debe actualizar el contador de aforo de manera atómica (ej. `UPDATE parking_quota SET available = available + 1`) para evitar inconsistencias si salen dos autos en el mismo milisegundo por carriles distintos.


### UC-ACC-09: Invalidar Invitaciones Expiradas y Cierre por Permanencia Anómala (`InvalidateExpiredInvitationsUseCase`)

* **Actor:** `System` (Proceso automático en segundo plano / *Cron Job*).
* **Descripción:** Proceso en lote (Batch) encargado de mantener la higiene, seguridad y consistencia del aforo en la base de datos. Se ejecuta diariamente durante la ventana de menor tráfico (ej. 3:00 AM). El sistema barre los registros buscando dos anomalías: 1) Invitaciones no utilizadas que deben expirar, y 2) Invitaciones en uso cuyo tiempo de estancia excede el límite permitido. Para estas últimas, el sistema aplica un cierre lógico pero retiene preventivamente el aforo vehicular hasta que exista una confirmación física humana, evitando la sobreventa de estacionamiento.
* **Comando de Entrada:** Ninguno a nivel de usuario. Se dispara internamente mediante un `ExpirePendingInvitationsCommand` (inyectando la `targetDate` para hacerlo idempotente).
* **Salida Esperada:** Actualización masiva de estados en la base de datos y un log técnico con la cantidad de registros afectados.
* **Eventos Disparados:** 
  * `InvitationExpiredEvent` (Para analítica del administrador. No notifica al residente).
  * `InvitationAutoCompletedEvent` (Registra el cierre forzado de la visita).
  * `ParkingAnomalyDetectedEvent` **(NUEVO):** Se dispara únicamente para vehículos con cierre forzado. Se envía a la `GuardConsole` y al panel del `CommunityAdmin` como una alerta de conciliación: *"Vehículo excedió tiempo máximo. Verifique físicamente el cajón para liberar el aforo"*.

* **Reglas de Negocio (Invariantes):**
  * *RN-ACC-09.1 - Limpieza de Pendientes (No Show):* El sistema transicionará de `PENDING` a `EXPIRED` todas las invitaciones cuya `scheduledDate` (más un periodo de gracia definido, ej. 24 horas) haya transcurrido sin registrar un ingreso.
  * *RN-ACC-09.2 - Cierre Lógico por Permanencia Anómala:* Para mantener la higiene de los accesos, el sistema transicionará de `IN_USE` a `AUTO_COMPLETED` a toda invitación que lleve más de 24 horas consecutivas al interior del recinto sin registrar salida.
  * *RN-ACC-09.3 - Retención Preventiva de Aforo (Fidelidad Física):* Al ejecutar el cierre lógico (RN-ACC-09.2), el sistema evaluará el `visitType`. Si la visita consumía estacionamiento (`VEHICLE`), el sistema **estrictamente NO sumará el espacio de vuelta al aforo disponible**. El espacio se mantendrá ocupado matemáticamente, asumiendo que el vehículo sigue físicamente ahí.
  * *RN-ACC-09.4 - Delegación de Conciliación:* La liberación real del aforo retenido requerirá que un guardia o administrador verifique físicamente el estacionamiento y ejecute un comando manual de conciliación desde su consola (resolviendo la alerta generada por el `ParkingAnomalyDetectedEvent`), asegurando que el software no invente lugares que físicamente están ocupados.

* **Consideraciones / Edge Cases (Técnicas):**
  * *Gestión de Zonas Horarias (Timezones):* La consulta debe cruzar la fecha de la invitación evaluándola contra el `timezone` configurado en el `CommunityContext` de cada fraccionamiento, para asegurar que la "madrugada" sea la hora local correcta para cada cliente.
  * *Rendimiento de Base de Datos (Batch Processing):* En un sistema multitenant, hacer un `UPDATE` masivo a las 3:00 AM puede bloquear tablas completas. El proceso debe ejecutarse de forma paginada (ej. lotes de 500 registros) y aprovechar un índice compuesto en las columnas `(status, scheduledDate)`.
  * *Idempotencia ante Caídas:* Si el servidor se reinicia a la mitad del proceso, el Job debe poder volver a ejecutarse sin causar inconsistencias lógicas ni afectar registros ya procesados.


## 5. Flujos de Ejecución Local (Offline Edge Computing)

### UC-ACC-10: Registrar Salida Offline (`RegisterOfflineDepartureUseCase`)

* **Actor:** `KioskApp` (Hardware en carril de salida operando sin conexión a la red central).
* **Descripción:** Flujo alterno y degradado para gestionar salidas durante una caída de red. La `KioskApp` escanea el `tokenPayload`. Al carecer de conexión, no consulta el estado de la `Invitation` en el backend. En su lugar, realiza una validación criptográfica o de formato estrictamente local para asegurar que el QR pertenece a AxolPass y al fraccionamiento actual. Si el token tiene el formato válido, concede la salida inmediatamente para priorizar el flujo vehicular. Registra el evento con su *timestamp* exacto en su almacenamiento local para ser conciliado posteriormente mediante el proceso de eventos diferidos (UC-ACC-05).
* **Comando de Entrada (Local):** `RegisterOfflineDepartureCommand` (`tokenPayload`, `exactTimestamp`).
* **Salida Esperada:** Apertura electromecánica inmediata de la barrera física (Objeto `ValidationResult` con status `GRANTED` a nivel local).
* **Eventos Disparados (Local):** `OfflineDepartureRegisteredEvent` (Activa el relé de la pluma y encola internamente un registro `DeferredAccessDTO` [Tipo: EXIT] con un UUID único).
* **Reglas de Negocio (Invariantes):**
  * *RN-10.1 - Prioridad de Evacuación (Ceguera de Estado):* A diferencia de una entrada, la `KioskApp` de salida **no buscará** el token en su caché local (descargado en el UC-ACC-04). Incluso si el token no está en el caché (porque se generó mientras la entrada estaba offline), la salida se concederá siempre que el formato del token sea válido.
  * *RN-10.2 - Autenticidad Local (Firma JWT/Criptografía):* Para evitar aperturas fraudulentas (ej. mostrar cualquier código QR), la `KioskApp` debe validar matemáticamente la firma del `tokenPayload` utilizando una llave pública almacenada en el hardware, garantizando que el código fue emitido genuinamente por el backend de AxolPass.
  * *RN-10.3 - Delegación de Cuota (`ParkingQuota`):* La `KioskApp` no intentará calcular ni liberar el aforo localmente. Esta responsabilidad se delega por completo al backend para cuando el evento sea sincronizado.


* **Consideraciones / Edge Cases (Técnicas):**
  * *Persistencia en Dispositivo (Local Storage Limits):* Si el fraccionamiento es masivo y la caída de red dura días, la tablet podría quedarse sin espacio de almacenamiento para guardar los eventos diferidos. En un escenario catastrófico de "Disco Lleno", el sistema debe sacrificar la auditoría pero **nunca** el flujo físico: la pluma debe seguir abriendo, descartando los registros más antiguos (FIFO) o simplemente dejando de grabar, pero alertando visualmente al guardia del fallo crítico de memoria.
  * *Prevención de Fraude Cíclico:* Si un residente malintencionado nota que la caseta no tiene internet, podría salir y entrar repetidamente usando un QR viejo fotocopiado. La tablet de salida debe guardar el hash del QR localmente por el resto del día para evitar que ese mismo código exacto abra la pluma de salida dos veces durante la misma contingencia.


### UC-ACC-11: Validar Acceso Offline (Contingencia de Entrada / `ValidateTokenOfflineUseCase`)

* **Actor:** `KioskApp` (Hardware en carril de entrada operando sin conexión).
* **Descripción:** Flujo alterno que se activa automáticamente tras un *timeout* de red o si el hardware detecta pérdida de conexión. La `KioskApp` lee el `tokenPayload` y primero valida su firma criptográfica. Luego, consulta su **almacenamiento local temporal** (poblado previamente por el UC-ACC-04) buscando el identificador del token. Verifica que el token esté registrado para el día de hoy, que su estado local sea `PENDING` y que la casa destino no esté suspendida por morosidad. Si todas las reglas locales se cumplen, concede el acceso, cambia el estado del token localmente a `IN_USE` y encola el evento de entrada para su futura sincronización.
* **Comando de Entrada (Local):** `ValidateOfflineTokenCommand` (`tokenPayload`, `exactTimestamp`).
* **Salida Esperada:** Objeto `ValidationResult` (status: `GRANTED` / `DENIED`, `reason`).
* **Eventos Disparados (Local):** `OfflineAccessGrantedEvent` (Activa el relé de la pluma de entrada y encola internamente un registro `DeferredAccessDTO` [Tipo: ENTRY] con un UUID único).
* **Reglas de Negocio (Invariantes):**
  * *RN-11.1 - Autenticidad Local (Firma JWT):* Antes de buscar en la base de datos local, el sistema debe validar la firma criptográfica del token usando la llave pública local para descartar códigos QR falsificados instantáneamente.
  * *RN-11.2 - Validación de Estado en Caché (Anti-passback local):* El token **debe existir** en el almacenamiento local y su `invitationStatus` debe ser obligatoriamente `PENDING`. Si el estado local es `IN_USE` o `CANCELED` (porque se descargó esa actualización antes de que se cayera la red, o porque ya se usó offline hace 5 minutos), el acceso se deniega.
  * *RN-11.3 - Bloqueo por Suspensión Offline:* Fiel a nuestra corrección en el UC-ACC-04, la `KioskApp` leerá el atributo `operationalStatus` del registro local. Si el valor es `SUSPENDED`, la tablet denegará el acceso y mostrará en la pantalla del guardia: *"Acceso Denegado: Casa Suspendida (Validación Offline)"*.
  * *RN-11.4 - Tolerancia de Aforo (`ParkingQuota` Blindness):* Al no tener conexión, la caseta de entrada no puede saber cuántos lugares de estacionamiento quedan realmente. En este escenario degradado, el sistema **ignorará el límite de aforo** y permitirá la entrada para no detener el tráfico en la avenida principal. (Esto empata perfectamente con la regla RN-05.4 que definimos, donde el backend aceptará aforos negativos temporalmente cuando regrese la red).


* **Consideraciones / Edge Cases (Técnicas):**
  * *El problema del "Cache Miss" (QR demasiado nuevo):* ¿Qué pasa si el residente generó la invitación con sus datos celulares (4G) a las 10:05 AM, pero la caseta perdió internet a las 10:00 AM? El visitante llega con un QR criptográficamente válido (pasa la RN-11.1), pero que **no existe** en el caché local de la tablet porque nunca se descargó.
  * *Resolución:* Por seguridad estricta, la `KioskApp` **denegará** el acceso automatizado ("QR no encontrado en caché de contingencia"). El visitante tendrá que pasar por el flujo de excepción con el guardia (**UC-ACC-06: Llegada Sorpresa**) para que el guardia valide visualmente al visitante y su destino.


## 6. Telemetría y Estado Global (IoT)

### UC-ACC-13: Sincronizar Estado Operativo Global (Heartbeat / `SyncOperationalStatusUseCase`)

* **Actor:** `KioskApp` y `GuardConsole` (Proceso de sistema en segundo plano).
* **Descripción:** Es el pulso vital ("Latido") de la caseta. Cada 60 segundos, el hardware envía una señal ligera al backend de Accesos para indicar que está encendido y operando. El backend responde entregando el estado global operativo del fraccionamiento (consultado previamente al *Contexto de Suscripción*). Si la respuesta indica un `CommercialBlackout` (Apagón Comercial por morosidad), la `KioskApp` y la `GuardConsole` entran inmediatamente en **Modo de Bloqueo Local**.
* **Comando de Entrada:** `SendHeartbeatCommand` (`kioskId` / `guardConsoleId`, `hardwareStatus` [OK, LOW_MEMORY, PRINTER_ERROR]).
* **Salida Esperada:** Objeto `OperationalStatusDTO` (`subscriptionStatus` [ACTIVE, SUSPENDED], `forceReboot` [Boolean], `serverTime`).
* **Eventos Disparados:** `HardwareHeartbeatReceivedEvent` (Para que el panel de Axolote Solutions sepa que las tablets de Oaxaca, por ejemplo, están en línea).
* **Reglas de Negocio (Invariantes):**
  * *RN-13.1 - Aplicación de Modo Bloqueo Local:* Si el `OperationalStatusDTO` devuelve `subscriptionStatus = SUSPENDED`, el hardware de la caseta **deshabilitará temporalmente** la validación de QRs de entrada (rechazando todo intento con el mensaje "Servicio Suspendido - Pase con el Guardia") y bloqueará la pantalla principal de la `GuardConsole`.
  * *RN-13.2 - Excepciones del Bloqueo Comercial:* Incluso bajo un estado `SUSPENDED`, el hardware mantendrá habilitados por ley los casos de uso:
    * **UC-ACC-07 / UC-ACC-12:** Botón de pánico y su justificación.
    * **UC-ACC-08 / UC-ACC-10:** Salidas vehiculares (Online y Offline).


  * *RN-13.3 - Persistencia del Castigo (Offline Blackout):* Si el último "Latido" exitoso indicó que el fraccionamiento estaba `SUSPENDED`, y un minuto después se cae el internet, el hardware **permanecerá en estado de bloqueo** durante toda la contingencia offline. No se puede evadir el pago simplemente desconectando el módem de la caseta.


* **Consideraciones / Edge Cases (Técnicas):**
  * *Sincronización de Relojes:* Este latido (Heartbeat) es el mecanismo perfecto para que la tablet ajuste su reloj interno (`serverTime`) con la hora atómica del servidor. Esto previene que un reinicio de la tablet desajuste su reloj y empiece a rechazar QRs por falsas expiraciones (UC-ACC-03/11).
  * ***[RESTRICCIÓN DE ARQUITECTURA Y COSTOS - IoT Transport Layer]:*** Para evitar la saturación del servidor y costos excesivos de infraestructura en la nube (ej. AWS/GCP), el "Latido" (Heartbeat) de 60 segundos **estrictamente no debe implementarse mediante peticiones HTTP/REST repetitivas (Polling)**. El equipo de desarrollo debe utilizar un protocolo de conexión persistente y ultra-ligero, siendo **MQTT** (Message Queuing Telemetry Transport) el estándar recomendado para el hardware, o en su defecto **WebSockets**. Esto reduce el ancho de banda a unos pocos bytes por mensaje y permite escalar a miles de casetas con un costo marginal mínimo.


### UC-ACC-19: Inicializar Entorno de Caseta (System-to-System)

* **Actor:** Sistema (Invocado automáticamente por el Bus de Eventos).
* **Descripción:** Flujo de infraestructura reactiva. Al consumir el evento `CommunitySettingsUpdatedEvent` emitido por el Contexto de Comunidad, este caso de uso inicializa las configuraciones operativas requeridas por el motor de accesos. Persiste la zona horaria (`timezone`) para la generación y validación de vigencias, e inicializa el contador de aforo (`ParkingQuota`) para la caseta.
* **Comando de Entrada:** `InitializeAccessEnvironmentCommand` (Construido internamente a partir del *payload* del evento).
  * `communityId` (ID del fraccionamiento).
  * `timezone` (Zona horaria para cálculos de expiración).
  * `maxVisitorParkingSpaces` (Valor inicial para el contador de aforo disponible).
* **Salida Esperada:** Configuración operativa de accesos inicializada con éxito.
* **Eventos Disparados:** Ninguno a nivel de dominio.

* **Reglas de Negocio (Invariantes):**
  * *RN-ACC-19.1 - Operación de Upsert (Actualización Continua):* Este caso de uso maneja tanto la primera inicialización como las actualizaciones futuras. Si el administrador del fraccionamiento cambia la configuración de estacionamiento meses después, este comando actualizará el techo del aforo sin reiniciar el conteo actual de vehículos adentro.
  * *RN-ACC-19.2 - Sincronización de Hardware:* Al ejecutarse este comando con éxito, el backend marcará una bandera interna de "Configuración Obsoleta" para que, en el siguiente "Latido" (`Heartbeat` - UC-ACC-13), la `KioskApp` y la `GuardConsole` físicas sepan que deben actualizar sus relojes y reglas locales.

### UC-ACC-21: Reaccionar a Suspensión de Unidad (`SyncHouseSuspensionUseCase`)

* **Actor:** `System` (Flujo disparado asíncronamente al consumir el `HouseOperationalStatusChangedEvent` desde el bus de mensajes).
* **Descripción:** Escucha los cambios de estado operativo de las casas administradas en el módulo de Comunidad. Si una casa es castigada (suspendida), purga inmediatamente sus accesos futuros para hacer efectiva la sanción física en la caseta y evitar que los residentes usen invitaciones creadas previamente.
* **Comando de Entrada:** `SyncHouseSuspensionCommand` (Construido a partir del payload del evento).
* **Salida Esperada:** Actualización masiva del estado de las invitaciones vinculadas a la casa.
* **Reglas de Negocio (Invariantes):**
  * *RN-ACC-21.1 - Purga de Invitaciones en Vuelo:* Si el `newStatus` del evento es `SUSPENDED`, el sistema buscará todas las `Invitation` asociadas a ese `houseId` cuyo estado actual sea `PENDING` y mutará su estado irreversiblemente a `CANCELED_BY_ADMIN`.
  * *RN-ACC-21.2 - Inmunidad de Visitas Activas:* Las invitaciones que ya se encuentren en estado `IN_USE` (visitantes que ya están físicamente adentro del fraccionamiento en ese momento) no serán alteradas, garantizando que puedan registrar su salida (`COMPLETED`) de forma normal y liberar el aforo global.
  * *RN-ACC-21.3 - Principio de No-Resurrección:* Si el `newStatus` del evento cambia de vuelta a `ACTIVE` (la casa regulariza su situación de morosidad), el sistema **NUNCA** revertirá el estado de las invitaciones previamente purgadas. El residente deberá generar nuevas invitaciones desde cero en su aplicación.

### UC-ACC-22: Procesar Fallo de Entrega de Invitación (`HandleInvitationDeliveryFailureUseCase`)

* **Actor:** `System` (Flujo asíncrono disparado al consumir el evento `AccessPassDeliveryFailedEvent`).
* **Descripción:** Reacciona cuando el motor de notificaciones reporta que fue imposible entregar el pase de acceso al visitante (por número inválido, rebote de correo, etc.). El sistema inhabilita la invitación para proteger la seguridad del recinto y notifica el estado al residente.
* **Comando de Entrada:** `HandleDeliveryFailureCommand` (Construido a partir del payload del evento).
* **Reglas de Negocio (Invariantes):**
  * *RN-ACC-22.1 - Mutación de Estado de Negocio:* El sistema cambiará el estado de la `Invitation` correspondiente de `PENDING` a `DELIVERY_FAILED`.
  * *RN-ACC-22.2 - Invalidation en Caseta:* Cualquier intento de escanear un token (QR) cuyo estado en la base de datos sea `DELIVERY_FAILED` será rechazado inmediatamente por la `KioskApp` o la `GuardConsole`, aplicando las mismas reglas de rechazo que una invitación expirada.
  * *RN-ACC-22.3 - Notificación de Retorno al Frontend:* Al consolidar el estado `DELIVERY_FAILED`, el sistema emitirá el evento local `InvitationStatusUpdatedEvent`. El motor de transporte del frontend consumirá este evento de forma síncrona y despachará la alerta visual a la aplicación móvil siguiendo la estrategia híbrida de alta disponibilidad (WebSockets / Push Fallback) y las políticas de escalabilidad horizontal especificadas formalmente en el [ADR-017](../../adr/ADR-017-estrategia-sincronizacion-tiempo-real.md).

