# Casos de uso.


## Anexo Arquitectonico: Automatización de pagos (Fase 2 - Congelado)

### UC-SUS-06: Procesar Webhook de Pago Exitoso (`ProcessSuccessfulPaymentWebhookUseCase`)

* **Actor:** `PaymentGateway` (Sistema externo operando asíncronamente, ej. Stripe, MercadoPago).
* **Descripción:** Flujo de integración de sistema a sistema (System-to-System) que recibe notificaciones cuando un cobro automatizado domiciliado se realiza con éxito. Valida la autenticidad criptográfica del mensaje, traduce los identificadores externos al dominio de AxolPass, asienta la transacción en el historial financiero y extiende la vigencia de la suscripción. Si la comunidad se encontraba en mora, orquesta su reactivación automática.
* **Comando de Entrada:** `ProcessSuccessfulPaymentWebhookCommand`
  * `rawPayload` (El cuerpo completo e inalterado de la petición JSON en formato texto, necesario para la validación de la firma).
  * `signatureHeader` (La firma criptográfica inyectada por la pasarela en los *headers* de la petición HTTP).
* **Salida Esperada:** Un código `HTTP 200 OK` rápido hacia la pasarela externa, junto con la actualización silenciosa de la vigencia en la base de datos local.
* **Eventos Disparados:**
  * `AutomatedPaymentRegisteredEvent` (Asienta el ingreso contable en el historial inmutable).
  * `PaymentReceiptGeneratedEvent` (Interceptado por Notificaciones para enviar la factura o recibo al administrador del fraccionamiento).
  * *(Condicional)*: Si el `status` de la suscripción antes de aplicar el pago era `SUSPENDED`, el sistema construye y despacha internamente un `ReactivateSubscriptionCommand` (asignando a `System` como `performerId` y "Pago automatizado recibido" como `reason`) para invocar el **UC-SUS-05: Reactivar Suscripción de Fraccionamiento** y liberar las casetas de forma inmediata.

* **Reglas de Negocio (Invariantes):**
  * *RN-SUS-06.1 - Verificación de Autenticidad (Zero Trust):* El sistema rechazará la petición inmediatamente (ej. `HTTP 401 Unauthorized`) si la validación criptográfica del `signatureHeader` contra el `rawPayload` utilizando el secreto compartido (*Webhook Secret*) falla. Esto mitiga ataques de inyección de pagos falsos.
  * *RN-SUS-06.2 - Traducción y Resolución de Entidad:* La pasarela no conoce el `communityId` de AxolPass, solo su propio identificador de cliente (ej. `cus_XYZ`). El sistema debe resolver a qué comunidad pertenece ese identificador. Si no existe un mapeo válido, el sistema registra una alerta crítica de consistencia y descarta el procesamiento.
  * *RN-SUS-06.3 - Idempotencia Estricta:* El sistema utilizará el identificador único de la transacción enviado por la pasarela (ej. `evt_XYZ`) como llave de idempotencia en la base de datos. Si un evento con ese identificador ya fue procesado exitosamente en el pasado, el sistema retornará `200 OK` de inmediato sin regalar días adicionales de vigencia ni duplicar los ingresos.

* **Consideraciones / Edge Cases (Técnicas):**
  * *Reconocimiento Ultra-rápido (Fast ACK vs. Background Processing):* Las pasarelas de pago suelen tener *timeouts* cortos (ej. 3 a 5 segundos). Si la base de datos de AxolPass está lenta, la pasarela podría creer que fallamos y reintentar el webhook horas después. Para evitarlo, el controlador de la API (Frontend del Backend) debe simplemente validar la firma criptográfica (RN-SUS-06.1), guardar el JSON bruto en una cola interna (Message Broker) y devolver un `200 OK` en menos de 200 milisegundos. Un proceso trabajador (*Worker*) consumirá la cola a su propio ritmo para ejecutar las RN-06.2 y RN-06.3 sin presión de red.


### UC-SUS-07: Configurar Método de Pago Domiciliado (`SetupPaymentMethodUseCase`)

* **Actor:** `CommunityAdmin` (Administrador del fraccionamiento, operando desde su panel de facturación).
* **Descripción:** Flujo de autoservicio (End-to-End) para registrar o actualizar la tarjeta de crédito/débito que se utilizará para la cobranza automatizada mensual o anual. En estricto cumplimiento normativo (ADR-013), el sistema actúa como orquestador en un flujo de tres fases:
  1. **Fase de Inicialización:** El Frontend solicita al Backend que prepare una sesión de pago. Como el Backend posee las credenciales secretas del procesador de pagos, se comunica con este para generar una "Sesión Segura" (`clientSecret`) y se la devuelve al Frontend.
  2. **Interacción Externa (Bypass):** El Frontend utiliza la Sesión Segura recibida para renderizar un formulario inyectado directamente por el procesador (Iframe). El usuario teclea su tarjeta ahí, saltándose nuestra infraestructura. El procesador de pagos valida la tarjeta y devuelve un Token seguro (`paymentToken`) al Frontend.
  3. **Fase de Persistencia:** El Frontend entrega el Token seguro al Backend para su almacenamiento y asociación final.

* **Comandos de Entrada (Flujo de 2 Pasos):**
  * *Paso 1 (Inicialización):* `InitiateSetupCommand`
    * `performerId` (ID del administrador, para autorización).
    * `communityId` (Identificador del fraccionamiento).
  * *Paso 2 (Persistencia):* `SavePaymentTokenCommand`
    * `performerId` (ID del administrador).
    * `communityId` (Identificador del fraccionamiento).
    * `paymentToken` (El token alfanumérico generado por el procesador de pagos, ej. `tok_123xyz`).

* **Salida Esperada:** * Del Paso 1: Un `clientSecret` para que el frontend dibuje el formulario.
  * Del Paso 2: Confirmación visual de guardado exitoso y actualización del perfil financiero.

* **Eventos Disparados:**
  * `PaymentMethodConfiguredEvent` (Disparado al finalizar el Paso 2. Actualiza el perfil para que las consultas de estado de cuenta muestren los datos públicos de la nueva tarjeta, ej. "Visa terminada en 4242").

* **Reglas de Negocio (Invariantes):**
  * *RN-SUS-07.1 - Autorización de Propiedad:* El sistema validará en ambos comandos que el `performerId` tenga los privilegios administrativos activos sobre el `communityId`.
  * *RN-SUS-07.2 - Delegación Absoluta PCI (Zero Data):* El backend jamás debe recibir el PAN (Número de Tarjeta) ni el CVV. Si en el Paso 2 el `paymentToken` tiene formato o longitud de tarjeta real, la petición será rechazada inmediatamente con un `HTTP 400 Bad Request`.
  * *RN-SUS-07.3 - Reemplazo de Método Previo:* Si la comunidad ya contaba con una tarjeta activa, la ejecución exitosa del Paso 2 reemplazará automáticamente el método de pago por defecto y notificará al procesador externo para desactivar el anterior.
  * *RN-SUS-07.4 - Verificación de Integridad:* El backend debe validar con el procesador de pagos que el `paymentToken` recibido en el Paso 2 sea auténtico, pertenezca al cliente correcto y esté respaldado por un método con fondos (ej. superando un micro-cargo o desafío 3D Secure).

* **Consideraciones / Edge Cases (Técnicas):**
  * *Sesiones Huérfanas:* Si el usuario ejecuta el Paso 1 pero cierra el navegador antes de completar el Paso 2, la sesión (`clientSecret`) caducará en el procesador externo (generalmente en 24 horas) sin afectar nuestro sistema local. No se requiere lógica de "rollback" (marcha atrás).
  * *Anexo Técnico:* Se recomienda a los desarrolladores consultar el diagrama de secuencia `uc-sus-07-tokenizacion-pci.puml` (o el anexo correspondiente) para visualizar el flujo exacto del SDK del procesador en el frontend.


#### Anexo Técnico al UC-SUS-07: Secuencia de Tokenización PCI-Compliant

Para garantizar el cumplimiento del ADR-013 (Cero almacenamiento de datos bancarios), los desarrolladores del Frontend y Backend deben implementar la siguiente coreografía de integración al momento de configurar un método de pago:

**1. Inicio de Sesión Segura (El Permiso):**
* El `CommunityAdmin` hace clic en "Agregar Método de Pago" en la interfaz (Frontend).
* El Frontend invoca el endpoint del `UC-SUS-07` en el Backend de AxolPass.
* El Backend de AxolPass se comunica con la API de la Pasarela (ej. Stripe) y solicita un `SetupIntent`.
* La Pasarela devuelve un `client_secret` temporal. El Backend entrega este secreto al Frontend.

**2. Renderizado del Iframe Inyectado (La Ilusión):**
* El Frontend utiliza el SDK oficial de la pasarela (ej. `Stripe.js`) inicializándolo con el `client_secret`.
* El SDK monta un componente visual (`iframe`) dentro del DOM de AxolPass.
* **Aviso Crítico:** Los campos donde el usuario teclea su tarjeta (PAN, CVV, Expiración) *no pertenecen* a AxolPass; están siendo servidos y escuchados directamente por los servidores de la Pasarela de Pagos.

**3. Captura y Tokenización (El Bypass):**
* El usuario completa los datos y hace clic en "Guardar Tarjeta".
* El `iframe` envía el número de tarjeta crudo directamente a la Pasarela. El Backend de AxolPass nunca ve esta petición.
* La Pasarela valida la tarjeta (con o sin desafío 3D Secure) y, si es exitosa, le devuelve al Frontend un **Token de Método de Pago** (ej. `pm_12345xyz`). Este token es un identificador seguro y sin valor para los hackers.

**4. Persistencia Local (Cierre del Ciclo):**
* El Frontend toma el Token (`pm_12345xyz`) y hace una segunda petición al Backend de AxolPass diciendo: *"El usuario configuró esta tarjeta exitosamente"*.
* El Backend de AxolPass guarda el Token en su base de datos, asociándolo al `communityId`.
* En futuros cobros automatizados (UC-SUS-06), el Backend simplemente le ordenará a la Pasarela: *"Realiza un cargo de $500 MXN al token `pm_12345xyz`"*.

### UC-SUS-08: Generar Orden de Pago para Mejora de Ciclo (`GenerateUpgradeOrderUseCase`)

* **Actor:** `CommunityAdmin` (Operando desde su panel de facturación) o `SystemAdmin` (Vía soporte).
* **Descripción:** Flujo transaccional que permite a un fraccionamiento formalizar su intención de mejorar su ciclo de facturación (ej. de mensual a anual). El sistema evalúa las reglas de negocio, aplica los descuentos comerciales correspondientes, y persiste en la base de datos una **Orden de Pago (Payment Order)** temporal. Esta orden actúa como una cotización formal y un recibo pre-pago con vigencia estricta.
* **Comando de Entrada:** `GenerateUpgradeOrderCommand`
  * `performerId` (ID de quien ejecuta la acción, para auditoría).
  * `communityId` (Identificador del fraccionamiento).
  * `targetBillingCycle` (La nueva frecuencia deseada, ej. `ANNUAL`).
* **Salida Esperada:** Confirmación de creación y entrega del objeto `PaymentOrderDTO` para renderizar el recibo en pantalla.
* **Eventos Disparados:**
  * `UpgradeOrderGeneratedEvent` (Notifica internamente que existe una orden de pago pendiente esperando ser conciliada por el equipo administrativo).

* **Reglas de Negocio (Invariantes):**
  * *RN-SUS-08.1 - Autorización Bimodal:* Validar que el `performerId` tenga los permisos adecuados sobre la comunidad.
  * *RN-SUS-08.2 - Restricción por Morosidad:* Si el estado de la suscripción es `SUSPENDED`, el sistema rechazará la creación de la orden.
  * *RN-SUS-08.3 - Periodo de Bloqueo Comercial (Lockout Window):* El sistema rechazará la solicitud con un `HTTP 409 Conflict` si la fecha actual está a **5 días o menos** de la `paidThroughDate`. Los cambios de ciclo deben solicitarse con anticipación para no interferir con el cierre de facturación en curso.
  * *RN-SUS-08.4 - Aplicación de Incentivo Comercial:* Al calcular el monto a pagar, el sistema aplicará las reglas de precio establecidas. (Ejemplo: Si el cambio es a `ANNUAL`, el sistema multiplicará el `basePrice` mensual por 10 en lugar de 12, otorgando el descuento estándar de la plataforma).
  * *RN-SUS-08.5 - Persistencia y Caducidad:* La orden generada se guardará en la base de datos local con estado `PENDING`, el monto calculado, la fecha de expiración de la cotización (ej. válida por 48 horas o hasta el `paidThroughDate`), y un `concept` de pago alfanumérico único (ej. `UPG-BOSQUES-26`). 
  * *RN-SUS-08.6 - Cambio Condicionado (No Pay, No Change):* La generación de esta Orden de Pago **no altera** el contrato ni el ciclo del fraccionamiento. La mejora se materializará únicamente cuando el `SystemAdmin` ejecute el **UC-SUS-04: Registrar Pago Manual** utilizando el concepto generado en esta orden.

* **Consideraciones / Edge Cases (Técnicas):**
  * *Limpieza de Órdenes Huérfanas:* Un proceso en segundo plano (Cron Job) deberá barrer la base de datos periódicamente para marcar como `EXPIRED` aquellas Órdenes de Pago cuyo tiempo de validez haya expirado sin recibir un pago, liberando el estado contable del cliente.



### UC-SUS-08.1: Modificar Frecuencia de Facturación (`UpdateBillingCycleUseCase`)

* **Actor:** `CommunityAdmin` (Operando desde su panel de facturación como autoservicio) o `SystemAdmin` (A petición del cliente vía soporte).
* **Descripción:** Flujo que permite modificar las condiciones de temporalidad del contrato de un fraccionamiento (ej. pasar de pagos mensuales a pagos anuales). El sistema recalcula la próxima fecha de corte y, si el fraccionamiento cuenta con una tarjeta domiciliada, notifica al procesador de pagos externo para que ajuste el periodo y el monto del cargo recurrente automático.
* **Comando de Entrada:** `ChangeBillingCycleCommand`
  * `performerId` (ID de quien ejecuta la acción, para auditoría).
  * `communityId` (Identificador del fraccionamiento).
  * `newBillingCycle` (La nueva frecuencia deseada, ej. `MONTHLY`, `ANNUAL`).
* **Salida Esperada:** Confirmación de actualización del ciclo de facturación.
* **Eventos Disparados:**
  * `BillingCycleChangedEvent` (Notifica el cambio de temporalidad, útil para los reportes de proyección financiera internos de Axolote Solutions).

* **Reglas de Negocio (Invariantes):**
  * *RN-SUS-08.1 - Autorización Bimodal:* El sistema validará que el `performerId` sea el administrador local del fraccionamiento o un administrador global de Axolote Solutions.
  * *RN-SUS-08.2 - Restricción por Morosidad:* Si el estado de la suscripción es `SUSPENDED` o `INACTIVE`, el sistema rechazará cualquier intento de cambio de ciclo hasta que el cliente liquide su adeudo pendiente y regrese al estado `ACTIVE`.
  * *RN-SUS-08.3 - Sincronización con Procesador Externo:* Si el fraccionamiento tiene un método de pago domiciliado activo (configurado vía `UC-SUS-07`), el backend de AxolPass debe notificar obligatoriamente al procesador de pagos (ej. actualizando la "Suscripción" en Stripe) sobre la nueva frecuencia, para que el siguiente cobro se ejecute correctamente.

* **Consideraciones / Edge Cases (Técnicas y de Negocio):**
  * *Efectividad Diferida (Sin Prorrateo):* Para evitar complejidades contables en el sistema, el cambio de frecuencia no surtirá efecto inmediato sobre el dinero ya pagado. Si un cliente mensual cambia a anual el día 15 del mes, el sistema esperará a que termine su mes actual y el cobro anual (con su respectivo monto) se ejecutará hasta el día 1 del siguiente ciclo.

