### Escenario de Prueba de Integración (E2E): Reenvío de Pase de Acceso Exitoso

* **ID de Prueba:** `INT-ACC-NOT-01`
* **Objetivo:** Verificar la correcta orquestación entre el API de Accesos, el Bus de Eventos y el Consumidor de Notificaciones al solicitar el reenvío manual de un pase vigente.

#### Precondiciones (Given)
1. Existe un Residente autenticado en el sistema (`residentId: R-123`).
2. Existe una Invitación (`invitationId: INV-999`) creada por el residente `R-123`.
3. El estado de la invitación `INV-999` en la base de datos es `ACTIVA` y su fecha de expiración es en el futuro.
4. El contador de reenvíos (`rate limit`) de la invitación en la última hora es `0`.
5. Los servicios de backend (Accesos, Notificaciones y el Message Broker) están en ejecución y conectados.

#### Acción (When)
1. El Residente envía un HTTP POST al endpoint de reenvío (ej. `/api/v1/invitations/INV-999/resend`).
2. El *payload* del comando (`RequestInvitationResendCommand`) especifica `targetMethod: WHATSAPP`.

#### Resultados Esperados (Then)

**Fase 1: Validación y Respuesta Síncrona (Contexto ACC)**
1. El Contexto de Accesos valida la propiedad de la invitación y que el estado sea `ACTIVO`.
2. El backend responde al cliente con un código HTTP `202 Accepted` indicando que la solicitud fue encolada.
3. El registro de límite de tasa (*rate limit*) en caché/BD para la `INV-999` se incrementa a `1`.

**Fase 2: Desacoplamiento (Bus de Eventos)**
1. El Contexto de Accesos publica exitosamente el evento `InvitationResendRequestedEvent` en el tópico/cola correspondiente.
2. El *payload* del evento contiene estrictamente los datos actualizados: `targetMethod`, `qrImageData` y `numericCode`.

**Fase 3: Consumo y Despacho Asíncrono (Contexto NOT)**
1. El Contexto de Notificaciones consume el evento del bus.
2. El `UC-NOT-06` genera el formato correcto para una *Media Template* de WhatsApp (sin enlaces web externos).
3. El sistema realiza una petición HTTP exitosa (Mockeada en el entorno de pruebas) a la API del proveedor de mensajería (ej. Meta/Twilio).
4. Se emite el evento de cierre `AccessPassResentEvent` para registrar el éxito en la auditoría del sistema.


### Especificación de Prueba E2E: Flujo Completo de Reenvío de Pase de Acceso

* **ID de Prueba:** `E2E-ACC-NOT-01`
* **Nivel:** End-to-End (UI Móvil -> API -> Event Bus -> Workers -> External API)
* **Objetivo:** Validar que un residente puede interactuar con la interfaz móvil para solicitar un reenvío, y que esta acción detona correctamente la orquestación de los casos de uso `UC-ACC-15` y `UC-NOT-06` hasta el despacho del mensaje.

#### Precondiciones (Given)
1. **Estado del Sistema:** Los servicios de Accesos, Notificaciones y el Message Broker están activos. Los endpoints externos (Meta API / SendGrid) están *mockeados* para interceptar peticiones en el entorno de pruebas.
2. **Estado de Datos (BD):** Existe el Residente "Raúl" (`residentId: R-123`) con una sesión activa en la app móvil. Existe una Invitación (`invitationId: INV-999`) a nombre de "Luis García", vinculada a Raúl, en estado `ACTIVA` y con límite de reenvíos en `0`.
3. **Estado de UI:** El emulador/dispositivo de prueba tiene la app AxolPass abierta en el Dashboard principal.

#### Acción (When)
1. El automatizador (o tester manual) navega a la pestaña **"Mis Invitaciones"**.
2. Selecciona la invitación de "Luis García" (`INV-999`) de la lista.
3. En la pantalla de detalle, hace tap en el botón **"Reenviar Pase"**.
4. En el modal emergente ("¿Por dónde quieres reenviar el pase?"), hace tap en la opción **"Reenviar por WhatsApp"**.

#### Resultados Esperados (Then)

**Fase 1: Respuesta de Interfaz y API (Validación UC-ACC-15)**
1. **UI Assertion:** La aplicación debe mostrar un *toast* o alerta verde con el texto: "Solicitud de reenvío aceptada".
2. **API Assertion:** La llamada de red (`POST /api/v1/invitations/INV-999/resend`) debe devolver un HTTP `202 Accepted`.
3. **DB Assertion:** El contador de reenvíos para la `INV-999` en la base de datos de Accesos debe incrementar a `1`.

**Fase 2: Orquestación Asíncrona (Event Bus)**
1. **Broker Assertion:** El bus de eventos debe registrar la publicación exitosa del evento `InvitationResendRequestedEvent`.
2. **Payload Assertion:** El evento debe contener `targetMethod: WHATSAPP`, el `numericCode` y el buffer/ruta del `qrImageData`.

**Fase 3: Consumo y Entrega (Validación UC-NOT-06)**
1. **Worker Assertion:** Los logs del servicio de Notificaciones deben indicar que el evento fue consumido exitosamente.
2. **External API Assertion:** El *mock* del proveedor externo (Meta/WhatsApp) debe registrar una petición HTTP entrante válida. Esta petición **debe** contener el formato de *Media Template*, la imagen del QR y **no debe** contener enlaces web (URLs) hacia el pase.
3. **Audit Assertion:** El sistema debe emitir el evento final `AccessPassResentEvent` para cerrar el ciclo de trazabilidad.

