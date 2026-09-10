# Casos de uso.

## Concepto Transversal: Política de Identidad y Accesos (IAM)

El contexto IAM de AxolPass es responsable de vincular identidades autenticadas externamente con cuentas internas o "cuentas sombra" (`globalUserId`), asignar privilegios operativos acotados por rol y comunidad (`CommunityScope`), y gobernar el ciclo de vida de las cuentas y sesiones de usuario. Toda la seguridad y autorización de la plataforma orbita alrededor de esta frontera de confianza.

Un elemento central de este gobierno es el **Estado de la Cuenta (`AccountStatus`)**. Las identidades no son simplemente "existentes" o "borradas", sino que transicionan por un ciclo de vida definido: desde un estado inicial inactivo de aprovisionamiento (`PENDING_ACTIVATION`), hacia una operación regular (`ACTIVE`), dejando la arquitectura preparada para futuras políticas de restricción (ej. `SUSPENDED` por investigaciones o morosidad, y `DISABLED` por baja corporativa definitiva).

## 1. Gestión de Identidad (Autenticación)

### UC-IAM-01: Registrar Identidad Externa (`RegisterExternalIdentityUseCase`)

* **Actor:** Sistema (Invocado automáticamente por el backend).
* **Descripción:** Es la puerta de entrada oficial a AxolPass. Ocurre inmediatamente después de que un usuario valida su teléfono o correo en la pantalla de login de la app (usando el proveedor externo como Firebase). Su único objetivo es tomar a ese usuario externo y darle un "acta de nacimiento" dentro de nuestra base de datos, asignándole un identificador interno (`globalUserId`). 
* **Comando de Entrada:** `RegisterExternalIdentityCommand`
  * `externalId` (El identificador único que nos entregó el proveedor externo, ej. el UID de Firebase).
  * `provider` (El nombre del proveedor, ej. 'FIREBASE').
  * `contactIdentifier` (El número de teléfono o correo electrónico del usuario).
* **Salida Esperada:** El `globalUserId` (Identificador interno de AxolPass).
* **Eventos Disparados:** `UserIdentityRegisteredEvent`.

* **Reglas de Negocio (Invariantes):**
  * *RN-IAM-01.1 - Idempotencia (Sin duplicados):* Si el `externalId` ya existe en nuestra base de datos (porque el usuario ya había entrado ayer, por ejemplo), el sistema no creará un registro nuevo; simplemente consultará y devolverá el `globalUserId` que se le asignó la primera vez.
  * *RN-IAM-01.2 - Confianza Delegada:* El sistema da por hecho que el `contactIdentifier` es real y ya fue verificado exitosamente por el proveedor externo (mediante el envío del SMS/OTP).
  * *RN-IAM-01.3 - Nacimiento sin Privilegios:* Registrar esta identidad no le otorga al usuario acceso a ninguna casa ni fraccionamiento. Solo lo registra en el catálogo general para que posteriormente un Administrador pueda buscarlo por su teléfono y darle acceso a una propiedad.

* **Consideraciones / Edge Cases:**
  * *Transparencia para el Usuario:* Este proceso dura milisegundos y el usuario humano jamás se entera de que ocurrió. Él solo ve la pantalla de "Cargando..." y luego entra a la app.


### UC-IAM-02: Provisionar Nueva Cuenta de Personal (`ProvisionNewStaffAccountUseCase`)

* **Actor:** `SystemAdmin` (para crear Administradores) o `CommunityAdmin` (para crear Guardias).
* **Descripción:** Flujo para dar de alta al personal operativo de la plataforma que aún no tiene cuenta. El sistema registra la identidad, se comunica con el proveedor externo (IdP) para generar una invitación segura y le asigna su rol inicial en una comunidad específica. Si el usuario ya existe, este caso de uso falla para evitar sobrescrituras accidentales.
* **Comando de Entrada:** `ProvisionNewStaffAccountCommand`
  * `performerId` (ID del administrador que ejecuta la acción).
  * `email` (Correo electrónico corporativo o personal del empleado).
  * `fullName` (Nombre completo del empleado).
  * `targetRole` (Rol a asignar: `COMMUNITY_ADMIN` o `SECURITY_GUARD`).
  * `communityId` (ID del fraccionamiento al que estará restringido).
* **Salida Esperada:** Identificador interno (`globalUserId`) y confirmación del resultado (Invitación enviada).
* **Eventos Disparados:** `NewStaffProvisionedEvent`.

* **Reglas de Negocio (Invariantes):**
  * *RN-IAM-02.1 - Jerarquía de Autorización Estricta:* Un `SystemAdmin` tiene el privilegio exclusivo de asignar el rol `COMMUNITY_ADMIN`. Un `CommunityAdmin` solo puede asignar el rol `SECURITY_GUARD` y estrictamente acotado a su propio `communityId`.
  * *RN-IAM-02.2 - Prevención de Duplicidad:* El sistema evaluará el `email`. Si ya existe una cuenta asociada, la operación será rechazada, delegando la acción al UC-IAM-03 para asignarle una nueva comunidad.
  * *RN-IAM-02.3 - Estado Inicial:* La cuenta nace con un estado inactivo (`PENDING_ACTIVATION`). El empleado no tendrá permisos operativos hasta que acepte la invitación del IdP y establezca sus credenciales.
  * *RN-IAM-02.4 - Delegación de Credenciales:* Bajo ninguna circunstancia AxolPass generará o almacenará contraseñas iniciales. El backend delegará esta acción al proveedor de identidad.

* **Consideraciones / Edge Cases:**
  * *Prevención de Escalada de Privilegios:* Validar que el `targetRole` coincida con el nivel de autoridad del `performerId`.

### UC-IAM-03: Asignar Comunidad a Personal Existente (`AssignCommunityToStaffUseCase`)

* **Actor:** `SystemAdmin`.
* **Descripción:** Flujo para vincular un administrador de fraccionamiento (`COMMUNITY_ADMIN`) que ya existe en el sistema a una nueva comunidad (ej. un administrador gestionando un segundo fraccionamiento). Este proceso omite la creación en el IdP, ya que la identidad ya está consolidada.
* **Comando de Entrada:** `AssignCommunityToStaffCommand`
  * `performerId` (ID del `SystemAdmin` que ejecuta la acción).
  * `globalUserId` (El ID del empleado existente).
  * `communityId` (ID del nuevo fraccionamiento a asignar).
* **Salida Esperada:** Confirmación de permiso actualizado y asignación exitosa.
* **Eventos Disparados:** `ExistingStaffAssignedEvent`.

* **Reglas de Negocio (Invariantes):**
  * *RN-IAM-03.1 - Autorización de Asignación:* Solo un `SystemAdmin` puede asignar múltiples comunidades a un mismo `COMMUNITY_ADMIN`. (Normalmente los guardias no cambian de caseta tan dinámicamente o pertenecen a un solo contexto).
  * *RN-IAM-03.2 - Validación de Existencia:* El sistema debe asegurar que el `globalUserId` pertenece a una cuenta de personal válida y activa.
  * *RN-IAM-03.3 - Notificación de Nueva Asignación:* Se disparará un correo informativo al usuario avisándole que tiene un nuevo fraccionamiento disponible en su panel.

* **Consideraciones / Edge Cases:**
  * *Gestión de Contexto (Frontend):* Al asignar múltiples `communityId` a un mismo usuario, la interfaz de usuario deberá implementar un "Context Switcher" para que el empleado seleccione sobre qué fraccionamiento desea operar al iniciar sesión.

## 2. Gestión y Ciclo de Vida de la Sesión

### UC-IAM-04: Revocar Sesión (`RevokeSessionUseCase`)

* **Actor:** Cualquier usuario autenticado (Acción manual) o el Sistema (Acción automática por inactividad o fin de turno).
* **Descripción:** Flujo que gestiona el ciclo de vida final de una sesión activa, destruyendo su validez para prevenir accesos no autorizados en equipos compartidos. Aplica políticas de caducidad diferenciadas según el rol del usuario y garantiza que las funciones de misión crítica (Botón de Pánico) permanezcan operativas en la caseta independientemente del estado de la sesión.
* **Comando de Entrada:** `RevokeSessionCommand`
  * `globalUserId` (El ID interno del usuario).
  * `accessToken` (El token de acceso actual).
  * `reason` (Motivo de la revocación: `MANUAL_LOGOUT`, `IDLE_TIMEOUT` o `SHIFT_ENDED`).
* **Salida Esperada:** Confirmación de cierre de sesión exitoso y limpieza del estado en el cliente.
* **Eventos Disparados:** `SessionRevokedEvent` (Para registros de auditoría de seguridad).

* **Reglas de Negocio (Invariantes):**
  * *RN-IAM-04.1 - Políticas de Timeout Diferenciadas por Rol:* 
    * **Administradores (`COMMUNITY_ADMIN`):** Se aplica un cierre de sesión automático estricto por inactividad (ej. 15 minutos sin interacción con el panel).
    * **Guardias (`SECURITY_GUARD`):** No se aplica cierre por inactividad. La sesión tiene una expiración absoluta ligada a la duración del turno (ej. 12 horas), obligando a un nuevo login solo al cambio de guardia.
  * *RN-IAM-04.2 - Revocación Integral:* La sesión debe ser invalidada tanto en el sistema local como en el proveedor de identidades externo de forma coordinada, bloqueando la emisión de nuevos accesos de forma definitiva.
  * *RN-IAM-04.3 - Excepción de Misión Crítica (Siempre Activo):* La caducidad, invalidación o ausencia de una sesión activa jamás debe bloquear la emisión de alertas de emergencia (Botón de Pánico) operadas desde una ubicación de caseta autorizada.

* **Consideraciones / Edge Cases (Técnicas):**
  * *Limpieza del Cliente:* El frontend (App móvil o Web) es responsable de destruir el token almacenado localmente de forma inmediata tras ejecutar este comando y redirigir al login (manteniendo visible el Botón de Emergencia en la pantalla de bloqueo).
  * *Sincronización con IdP:* Para cumplir la RN-IAM-04.2, el backend utilizará el SDK del proveedor (ej. Firebase/Supabase) para revocar los *Refresh Tokens*.
  * *Bypass de Filtros (Spring Security):* Para cumplir la RN-IAM-04.3, el framework de seguridad configurará el endpoint del Botón de Pánico para evadir la validación del JWT del usuario, requiriendo en su lugar autenticación a nivel de dispositivo (ej. *API Key* estática de la tablet).
  * *Lista Negra de Tokens (Redis) [Opcional para MVP]:* Dado que los JWT son *stateless*, opcionalmente se colocará la firma del token revocado en una lista negra temporal en memoria para rechazar peticiones inmediatas si el token fue extraído maliciosamente.
  * *Prevención de Abuso de Emergencias:* Dado que el endpoint de emergencia no pide sesión de usuario, el backend debe implementar un límite de peticiones (*Rate Limiting*) por dispositivo para evitar spam accidental o malicioso.
