# Ubiquitous Language Glossary - AxolPass (English-First)

## 1. Identity & Access Management (IAM Context)

* **`SystemAdmin`:** Personal interno de Axolote Solutions con permisos globales para gestionar clientes y facturación. En el código, es el superusuario.
* **`CommunityAdmin`:** El administrador local del fraccionamiento. Configura las reglas y gestiona las casas, pero su alcance está estrictamente limitado a su propia comunidad.
* **`PrimaryResident`:** El responsable legal u operativo de una `House` (Propietario o Inquilino). Tiene la autoridad para delegar accesos creando `SecondaryResidents`.
* **`SecondaryResident`:** Familiares o *roomies* asociados a una `House` específica. Pueden generar `Invitations`, pero no pueden administrar la casa.
* **`SecurityGuard`:** El personal físico en caseta que opera la `GuardConsole`, recibe alertas y gestiona contingencias operativas.
* **`globalUserId` (Cuenta Sombra):** Identificador interno único en AxolPass que vincula una identidad autenticada externamente con sus roles, historial y permisos operativos dentro de la plataforma.
* **`AccountStatus` (Estado de la Cuenta):** Concepto transversal que rige el ciclo de vida de un `globalUserId` (ej. `PENDING_ACTIVATION`, `ACTIVE`, `SUSPENDED`, `DISABLED`), determinando la capacidad a nivel sistema de la identidad para iniciar y mantener sesiones.
* **`CommunityScope` (Alcance de Comunidad):** El límite lógico de autorización que acota los privilegios de un usuario. Garantiza que un `CommunityAdmin` o `SecurityGuard` solo pueda ejercer sus funciones dentro del fraccionamiento específico que le fue asignado.
* **`IdentityProvider / IdP` (Proveedor de Identidad):** Servicio externo (ej. Firebase, Supabase) responsable de gestionar de manera segura las credenciales (passwords/OTP) y la emisión de tokens de sesión, delegando a AxolPass estrictamente la autorización del negocio.

## 2. Community Management Context

* **`Community` (Fraccionamiento):** El cliente B2B y el límite físico principal. Agrupa casas, residentes y dicta las reglas globales (como la cuota de estacionamiento).
* **`House` (Casa / Lote):** La unidad física dentro de la `Community`. Es la entidad a la que se le asignan los límites de invitaciones y los estados operativos.
* **`HouseModel` (Modelo de Propiedad):** Prototipo o molde arquitectónico que define los atributos físicos compartidos (ej. lugares de estacionamiento) que las unidades privativas heredan dinámicamente.
* **`Tenancy` / `Residency` (Vínculo de Residencia):** El contrato lógico e histórico entre un usuario (`globalUserId`) y una `House`, que le otorga el rol de residente (Primario o Secundario) y establece sus derechos operativos sobre la propiedad.
* **`CommunitySettings` (Reglas de Comunidad):** La configuración fundacional de un fraccionamiento que dicta los parámetros operativos globales, como la zona horaria local (`timezone`), el aforo máximo de estacionamiento para visitas y las cuotas de invitaciones.
* **`OperationalStatus` (Estado Operativo):** Propiedad de una `House` que determina si sus residentes tienen derechos vigentes para operar en el sistema. Sus valores son *Active*, *Inactive* (borrado lógico) o *Suspended* (bloqueo temporal por morosidad financiera, sanciones administrativas o mal uso). **Nota MVP:** Las transiciones operativas soportadas por la plataforma son únicamente *Active* ↔ *Suspended*. El estado *Inactive* queda reservado para futuras capacidades de baja lógica.
* **`ResidentTransfer` (Traspaso Formal):** El proceso administrativo mediante el cual el `CommunityAdmin` retira a un `PrimaryResident` de una `House` (ej. por mudanza), invalidando en cascada a sus usuarios secundarios y visitas pendientes.
* **`Break-Glass` (Auditoría Forense):** Mecanismo de excepción que permite vulnerar el velo de privacidad (ej. anonimización de visitantes) obligando a ingresar una justificación estricta que queda registrada de forma inmutable en el `AuditLog`.

## 3. Access & Visitor Control Context

* **`Guest` (Visitante):** La persona externa que requiere cruzar la barrera. Definida temporalmente por su nombre y vehículo.
* **`Invitation`:** El registro lógico creado por un residente que autoriza a un `Guest` a ingresar. Su ciclo de vida incluye estados como: *Pending*, *In Use*, *Completed* (salida nominal), *Auto-Completed* (cierre forzado por el sistema o anti-tailgating), *Expired*, *Canceled*, *CANCELED_BY_ADMIN* o *DELIVERY_FAILED*.
* **`DELIVERY_FAILED`:** Estado del ciclo de vida de una invitación que indica que el motor de notificaciones no pudo entregar el pase al visitante debido a un error permanente del proveedor externo.
* **`CANCELED_BY_ADMIN`:** Estado irreversible de una invitación que fue revocada automáticamente por el sistema como daño colateral al suspender administrativamente la casa destino.
* **`AccessToken` (Token QR/PIN):** La credencial física o digital (payload) generada a partir de una `Invitation` que el `Guest` presenta en la caseta.
* **`ParkingQuota` (Aforo de Estacionamiento):** El contador en tiempo real de espacios disponibles para invitados dentro de la `Community`.
* **`GuardConsole` (Consola del Guardia):** La interfaz de software (SPA/Electron) utilizada por el `SecurityGuard` dentro de la caseta para ver alertas, justificar aperturas físicas y registrar excepciones.
* **`KioskApp` (App de Caseta / Tótem):** La aplicación (React Native/Android) instalada en la tablet orientada hacia el visitante. Lee los `AccessTokens`, mantiene el caché offline y envía la señal eléctrica a la barrera.
* **`UnscheduledVisit` (Llegada Sorpresa):** El evento donde un `Guest` se presenta en caseta sin una `Invitation` previa, requiriendo que el `SecurityGuard` capture sus datos manualmente en la `GuardConsole`.
* **`EmergencyOverride` (Apertura de Emergencia):** El evento crítico donde el `SecurityGuard` fuerza la apertura de la barrera desde la `GuardConsole` (por falla de hardware o emergencia médica), requiriendo una justificación obligatoria para la auditoría.

## 4. Billing & Audit Context

* **`Subscription` (Suscripción / Contrato Duro):** El acuerdo comercial inmutable entre Axolote Solutions y la `Community` que establece las condiciones operativas y financieras base, tales como la capacidad total de casas, el límite de cuentas por casa (`maxAccountsPerHouse`), la tarifa, la vigencia legal y el ciclo de facturación (que puede ser mensual o anual).
* **`SubscriptionStatus` (Estado de Suscripción):** El estado operativo del contrato a nivel sistema. Puede ser *Active*, *Pending Start* (contrato firmado pero en espera de fecha de inicio), *Suspended* (morosidad) o *Inactive* (cancelado/borrado lógico).
* **`totalCapacity` (Capacidad Total):** El número máximo de unidades privativas (casas/lotes) amparadas inmutablemente por el contrato de la suscripción.
* **`basePrice` & `billingCycle` (Tarifa y Ciclo):** El monto exacto a cobrar y la frecuencia de la facturación comercial (ej. `MONTHLY`, `ANNUAL`).
* **`contractStartDate` & `contractEndDate`:** Fechas que delimitan el inicio de operaciones y la expiración del amparo legal del contrato firmado.
* **`paidThroughDate` (Fecha de Vigencia):** La fecha cronológica exacta hasta la cual el fraccionamiento ha pagado su servicio. Si esta fecha es superada por la fecha actual, se detona la suspensión automática del servicio.
* **`PaymentMethod` / `paymentToken`:** El token opaco devuelto por la pasarela de pagos externa (ej. Stripe) que representa la tarjeta del cliente, garantizando el cumplimiento PCI-DSS al no tocar ni almacenar datos bancarios en el backend.
* **`referenceNumber` / `concept`:** Identificadores alfanuméricos (folio SPEI o clave de rastreo) utilizados para garantizar la unicidad y conciliación de los pagos manuales.
* **`GracePeriod` (Periodo de Gracia):** Extensión temporal (ej. 72 horas) de la `paidThroughDate` otorgada administrativamente para evitar la interrupción operativa mientras un pago en tránsito se concilia, sin alterar el saldo contable.
* **`FinancialProfile` (Perfil Financiero):** Agregado del dominio que encapsula la información de tarifas y las reglas matemáticas para calcular ciclos de facturación y límites legales, protegiendo la integridad transaccional.
* **`PaymentRecord` (Registro de Pago):** Entidad inmutable que asienta un ingreso bancario (manual o automatizado) validado en el sistema, vinculando el `referenceNumber` con la extensión de la suscripción.
* **`PaymentOrder` (Orden de Pago):** Documento transaccional temporal (cotización) generado cuando un fraccionamiento desea modificar su ciclo de facturación, amparando un monto calculado con reglas comerciales específicas.
* **`CancellationType` (Motivo de Baja):** Clasificador estricto (ej. `DATA_ENTRY_ERROR`, `CONTRACT_NOT_RENEWED`) que rige bajo qué condiciones deterministas es permitido mutar un contrato comercial a estado `INACTIVE`.
* **`AuditLog` / `AuditEvent`:** Un registro inmutable de cualquier acción crítica (alta de casa, acceso de visitante, `EmergencyOverride`) que incluye el timestamp, el actor y el resultado.
* **`UnjustifiedSecurityIncident` (Incidente de Seguridad no Justificado):** Clasificación de auditoría inmutable asignada automáticamente por el sistema a un `EmergencyOverride` (Apertura de Emergencia) que el `SecurityGuard` omitió justificar dentro del periodo de gracia. Funciona como una alerta operativa estricta para el `CommunityAdmin`.

## 5. Notifications & Alerts Context

* **`NotificationPolicy` (Política de Notificación):** El conjunto de reglas unificadas que gobiernan el despacho de mensajes en el sistema, abarcando la resolución dinámica de destinatarios, preferencias de usuario (opt-out), prioridad de entrega, caducidad (TTL), canal y estrategia de reintentos (resiliencia).
* **`CriticalAlert` (Alerta Crítica):** Un mensaje de emergencia transaccional (ej. detonación de botón de pánico) que ignora deliberadamente las preferencias de silencio del receptor (No-Opt-Out) y se despacha con prioridad máxima a los perfiles de respuesta operativa inmediata.
* **`AccessPass` (Pase de Acceso):** La representación visual e informativa de una `Invitation` enviada al visitante a través de canales externos (WhatsApp o Correo Electrónico), la cual incluye incrustado el `AccessToken` (QR y NIP numérico) y no depende de enlaces web.
* **`DeviceToken`:** Identificador técnico emitido por proveedores en la nube (ej. APNs/FCM) que representa de forma unívoca un dispositivo móvil capaz de recibir notificaciones Push. El motor de notificaciones realiza un mantenimiento proactivo purgando los tokens que caducan o se reportan como inválidos.
* **`DeliveryChannel` (Canal de Entrega):** El medio tecnológico seleccionado para despachar un mensaje o `AccessPass` (Push, Correo Electrónico, WhatsApp), determinado por las reglas del caso de uso o la elección del usuario.
* **`NotificationPreference` (Preferencia de Notificación):** La configuración granular (Opt-out) definida por el residente que determina qué tipos de alertas rutinarias desea silenciar o recibir. Estas preferencias son respetadas por la `NotificationPolicy` general, pero ignoradas obligatoriamente ante una `CriticalAlert`.
* **`OfflineOverrideAlert` (Alerta de Desfase Offline):** Comunicación proactiva de transparencia enviada al residente cuando la realidad física en la caseta (ej. un ingreso autorizado localmente sin internet) prevalece sobre una instrucción digital (ej. una cancelación en la nube). Su propósito de negocio es evitar que el usuario perciba este desfase de sincronización temporal como una vulnerabilidad de seguridad de la plataforma.
* **`Mensajería FIFO Agrupada`:** Patrón de infraestructura en el Message Broker que garantiza que los eventos de una misma entidad se procesen en el orden cronológico exacto en que ocurrieron, evitando condiciones de carrera (ej. recibir cancelación antes que la invitación original).
* **`Sincronización Delta (Pull)`:** Patrón de resiliencia del frontend móvil que consulta los últimos cambios de estado de sus entidades al recuperar la conexión, complementando a WebSockets/Push.
