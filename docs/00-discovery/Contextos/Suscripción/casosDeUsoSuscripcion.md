# Casos de uso.

## 1. Onboarding y Alta de Fraccionamientos (Backoffice Flow)

### UC-SUS-01: Aprovisionar Nuevo Fraccionamiento (`ProvisionCommunityUseCase`)

* **Actor:** `SystemAdmin` (Personal interno de Axolote Solutions operando desde el panel de administración global).
* **Descripción:** Flujo maestro (Comando) que registra a un nuevo cliente B2B en la plataforma tras la firma de un contrato. Este caso de uso crea el espacio lógico de la comunidad, registra su ubicación, establece sus métricas de contrato inmutables (capacidad, precio y vigencia legal), define su preferencia inicial de facturación y orquesta la creación o vinculación de la cuenta del administrador local delegando la responsabilidad de identidad.

* **Comando de Entrada:** `ProvisionCommunityCommand`
  * `systemAdminId` (ID del empleado de Axolote que ejecuta la acción, para auditoría).
  * `communityName` (Nombre del fraccionamiento).
  * `address` (Dirección postal estructurada del fraccionamiento):
    * `street` (Calle, avenida, carretera o vía).
    * `externalNumber` (Número exterior).
    * `internalNumber` (Número interior, local, edificio u otra referencia interna. Opcional).
    * `neighborhood` (Colonia o asentamiento).
    * `postalCode` (Código postal de cinco dígitos).
    * `municipality` (Municipio o alcaldía).
    * `city` (Ciudad o localidad).
    * `state` (Entidad federativa).
    * `country` (País. Para el alcance del MVP su valor es fijo: `MX`).
  * `totalCapacity` (Número total de unidades privativas / lotes amparados por el contrato).
  * `basePrice` (Monto tarifario exacto correspondiente a un (1) ciclo de facturación completo, sea este mensual o anual).
  * `billingCycle` (Frecuencia de facturación comercial inicial, ej. `MONTHLY`, `ANNUAL`. Para el alcance del MVP, una vez asignado, este valor será inmutable).
  * **`contractStartDate`** (Fecha acordada para el inicio de operaciones y cobro).
  * `contractEndDate` (Fecha exacta en la que expira el amparo legal del contrato firmado).
  * `maxAccountsPerHouse` (Límite comercial de cuentas móviles permitidas por cada unidad privativa, definido en la negociación).
  * `adminFullName` (Nombre del administrador del fraccionamiento).
  * `adminEmail` (Correo electrónico del administrador del fraccionamiento).

* **Salida Esperada:** Confirmación de creación exitosa con el `communityId` generado.

* **Eventos Disparados:**
  * `CommunityProvisionedEvent` (Interceptado por el Contexto de Comunidad para ejecutar el **UC-COM-09: Inicializar Entorno de Comunidad**, el cual crea el registro raíz de la comunidad y fija la capacidad máxima permitida).
  * `CommunityAdminAssignedEvent` (Interceptado por el Contexto IAM para ejecutar el **UC-IAM-02: Provisionar Cuenta de Personal**, el cual orquestará la creación o vinculación en el IdP y disparará el correo de bienvenida).

* **Reglas de Negocio (Invariantes):**
  * *RN-SUS-01.1 - Autorización Estricta:* El sistema rechazará la petición con un `HTTP 403 Forbidden` si el usuario que invoca el comando no tiene el rol global de `SystemAdmin`.

  * *RN-SUS-01.2 - Límite de Diferimiento de Inicio:* El sistema validará estrictamente que la `contractStartDate` sea mayor o igual a la fecha actual (`>= HOY`) y no superior a **90 días naturales** a partir de la fecha actual. Bajo ninguna circunstancia se permitirá el aprovisionamiento con fechas en el pasado, garantizando que al cliente no se le cobre por tiempo donde el software no existía.

  * *RN-SUS-01.3 - Inmutabilidad del Contrato:* El sistema registrará `totalCapacity`, `basePrice`, `contractStartDate`, `contractEndDate` y `maxAccountsPerHouse` como el "Contrato Duro" inmutable. De igual forma, para el alcance del MVP, `billingCycle` se registrará como un valor inicial estático (ej. `MONTHLY`). Ni el cliente ni el sistema expondrán endpoints para alterar estas condiciones dinámicamente (cualquier error de captura exigirá cancelación y reaprovisionamiento, y las mejoras de ciclo quedan para iteraciones futuras).

  * *RN-SUS-01.4 - Delegación de Identidad (Multi-Comunidad):* El Contexto de Suscripción no interactuará con el Identity Provider (IdP). Simplemente empaquetará el correo y nombre en el evento de salida para que el **UC-IAM-02** se encargue de la identidad.

  * *RN-SUS-01.5 - Asignación de Estado y Vigencia Financiera:*
    * Si la `contractStartDate` es exactamente igual a la fecha actual **(HOY)**, el sistema nacerá con estado `ACTIVE` (Día 0 de operación, habilitado para que el administrador comience su configuración inicial).
    * Si la `contractStartDate` es futura, el sistema nacerá con estado `PENDING_START` (sin privilegios operativos en caseta).
    * En ambos casos, el sistema calculará la fecha de término del primer periodo (`paidThroughDate`) sumando la duración del `billingCycle` **a partir del `contractStartDate`**, garantizando que el cliente reciba los días exactos contratados.

  * *RN-SUS-01.6 - Dirección Postal en México:* La dirección del fraccionamiento debe registrarse de forma estructurada. `street`, `externalNumber`, `neighborhood`, `postalCode`, `municipality`, `city` y `state` son requeridos. `internalNumber` es opcional. `postalCode` debe contener exactamente cinco dígitos. Para el alcance del MVP, `country` tendrá siempre el valor `MX` y no podrá ser especificado con un valor diferente.

* **Consideraciones / Edge Cases:**
  * *Aislamiento de Datos:* Al disparar el evento, la base de datos debe asegurar que se generen los esquemas o identificadores necesarios para aislar los datos de este nuevo cliente de forma criptográficamente segura.
  * *Activación Automática:* Si la comunidad nace como `PENDING_START`, el sistema delega la responsabilidad de "encenderla" en el futuro al **UC-SUS-12: Activar Comunidad Programada**, manteniendo el principio de responsabilidad única.
  * En ambos casos, el sistema calculará la vigencia del primer periodo (`paidThroughDate`) sumando la duración temporal de un (1) `billingCycle` a partir de la `contractStartDate`, garantizando que el reloj de cobro sea matemáticamente exacto.

### UC-SUS-02: Consultar Directorio de Fraccionamientos (`ListCommunitiesQuery`)

* **Actor:** `SystemAdmin` (Personal interno de Axolote Solutions operando desde el panel de administración global).
* **Descripción:** Flujo de lectura paginada (Query). Alimenta la pantalla principal del Backoffice de Axolote Solutions. Permite al equipo administrativo visualizar una lista de todos los clientes (fraccionamientos) dados de alta en la plataforma, buscar clientes específicos y monitorear rápidamente su estado de suscripción comercial (activos o suspendidos por morosidad).
* **Consulta de Entrada:** `GetCommunitiesQuery`
  * `systemAdminId` (ID del empleado de Axolote, para autorización).
  * `limit` (Paginación: cantidad de registros por página, ej. 50).
  * `offset` (Paginación: punto de inicio).
  * `statusFilter` (Opcional: Para buscar solo fraccionamientos en estado `ACTIVE`, `SUSPENDED` o `INACTIVE`).
  * `searchTerm` (Opcional: Cadena de texto para buscar por nombre del fraccionamiento o correo del administrador).
* **Salida Esperada:** `PaginatedList<CommunitySummaryDTO>`. Cada objeto de la lista contiene datos ligeros y sumarizados: `communityId`, `communityName`, `totalCapacity`, `adminEmail`, `subscriptionStatus` y `createdAt` (Fecha de alta).
* **Eventos Disparados:** Ninguno. (Por ser una operación de solo lectura, no muta el estado ni genera eventos de dominio).

* **Reglas de Consulta (Filtros y Autorización):**
  * *RQ-SUS-02.1 - Autorización Global Estricta:* El sistema debe verificar rigurosamente que el `systemAdminId` pertenezca a un usuario con el rol global de `SystemAdmin`. Si un `CommunityAdmin` intenta consumir este endpoint, el sistema lo rechazará con un `HTTP 403 Forbidden` (Prevención de escalada de privilegios y fuga de datos B2B).
  * *RQ-SUS-02.2 - Proyección Ligera (Lazy Loading):* Para garantizar un tiempo de respuesta óptimo (menor a 200ms), esta consulta **no devolverá** información pesada como el historial de pagos, facturas, ni los datos de la pasarela de pagos. Esos detalles se consultarán únicamente cuando el usuario decida ver el detalle individual de un fraccionamiento.
  * *RQ-SUS-02.3 - Búsqueda Flexible:* Si se provee un `searchTerm`, el sistema ejecutará una búsqueda insensible a mayúsculas y acentos (*Case/Accent Insensitive*) sobre los campos `communityName` y `adminEmail`, facilitando al equipo de soporte encontrar rápidamente a un cliente que llama por teléfono.

* **Consideraciones / Edge Cases (Técnicas):**
  * *Ordenamiento por Defecto:* Para mejor experiencia de usuario en el panel de control, los resultados deben ordenarse por defecto mostrando primero los fraccionamientos creados más recientemente (`ORDER BY createdAt DESC`), permitiendo al equipo de ventas/onboarding ver sus cierres más nuevos de inmediato.
  * *Rendimiento de Búsqueda:* Si la búsqueda por texto (`searchTerm`) se vuelve lenta cuando la base de datos crezca, el equipo técnico deberá considerar agregar índices de texto completo (ej. `GIN/GIST` en PostgreSQL) sobre las columnas de nombre y correo.

### UC-SUS-03: Consultar Detalle y Estado de Cuenta de Fraccionamiento (`GetCommunityDetailQuery`)

* **Actor:** `SystemAdmin` (Personal interno de Axolote Solutions operando desde el panel de administración global).
* **Descripción:** Flujo de lectura profunda (Query). Permite al equipo de administración global visualizar el panorama completo y detallado de un cliente específico. A diferencia del listado general, esta consulta agrupa y devuelve la información demográfica del fraccionamiento, el estado exacto de su suscripción actual (vigencia, periodo de gracia, próximo corte), el método de pago tokenizado (si aplica) y el historial de pagos o facturas.
* **Consulta de Entrada:** `GetCommunityDetailQuery`
  * `systemAdminId` (ID del empleado de Axolote, para autorización).
  * `communityId` (Identificador único del fraccionamiento a consultar).
* **Salida Esperada:** Objeto `CommunityDetailDTO`. Contiene sub-objetos estructurados: 
  * `Profile`: Datos base (`communityName`, `address`, `totalCapacity`, `adminEmail`).
  * `Subscription`: (`status` [ACTIVE, SUSPENDED, INACTIVE], `currentPeriodStart`, `paidThroughDate`, `planType`).
  * `PaymentMethod`: Datos enmascarados de la pasarela (`brand` ej. "Visa", `last4` ej. "4242", `expMonth`, `expYear`).
  * `BillingHistory`: Lista de transacciones o facturas pasadas.
* **Eventos Disparados:** Ninguno. (Operación de solo lectura).

* **Reglas de Consulta (Filtros y Autorización):**
  * *RQ-SUS-03.1 - Autorización Global Estricta:* Al igual que en el directorio, el sistema validará que el `systemAdminId` posea el rol de `SystemAdmin`. 
  * *RQ-SUS-03.2 - Cumplimiento PCI Visual (Enmascaramiento):* En cumplimiento estricto con el ADR-013, la respuesta del sistema **solo debe contener** la información pública de la tarjeta (los últimos 4 dígitos y la marca). El backend jamás intentará consultar o recuperar el PAN completo desde la pasarela para mostrarlo en pantalla.
  * *RQ-SUS-03.3 - Tolerancia a Falta de Método de Pago:* Si el fraccionamiento aún realiza pagos de forma manual (transferencia SPEI) y no tiene una tarjeta domiciliada en la pasarela externa, el campo `PaymentMethod` será nulo. La consulta debe completarse exitosamente y no fallar por esta ausencia.
  * *RQ-SUS-03.4 - Inmutabilidad Histórica:* El historial de facturación (`BillingHistory`) debe mostrarse completo independientemente del estado actual del fraccionamiento (incluso si está `SUSPENDED` o `INACTIVE`), para garantizar trazabilidad contable en auditorías futuras.

* **Consideraciones / Edge Cases (Técnicas):**
  * *Fuente de Verdad (Caché vs Tiempo Real):* Dado que el estado de los pagos ocurre en una pasarela externa (ej. Stripe), este caso de uso consultará **exclusivamente la base de datos local** de AxolPass (la cual es alimentada asíncronamente por los Webhooks de la pasarela). Esto garantiza que la consulta sea ultrarrápida y no dependa de la latencia de red hacia los servidores del proveedor de pagos al momento de cargar la pantalla.

## 2. Gestión de Pagos y Morosidad (Billing Flow)

### UC-SUS-04: Registrar Pago Manual (`RegisterManualPaymentUseCase`)

* **Actor:** `SystemAdmin` (Personal interno de Axolote Solutions operando desde el panel de administración global).
* **Descripción:** Flujo transaccional responsable de registrar oficialmente un ingreso bancario (SPEI o depósito). El sistema recibe los datos del pago, recupera el perfil financiero de la comunidad, calcula matemáticamente los ciclos cubiertos por el monto, avanza la fecha de vigencia (`paidThroughDate`) garantizando el cobro de la deuda histórica, y guarda toda la operación en una única transacción segura (ACID).
* **Comando de Entrada:** `RegisterManualPaymentCommand`
  * `systemAdminId` (ID del empleado de Axolote, para auditoría).
  * `communityId` (Identificador de la comunidad).
  * `amount` (Monto exacto total recibido).
  * `paymentDate` (Fecha de acreditación en el banco).
  * `referenceNumber` (Clave de rastreo SPEI o número de folio).
  * `notes` (Opcional: Comentarios internos).
* **Salida Esperada:** Confirmación de registro exitoso devolviendo el nuevo estado de la suscripción.
* **Eventos Disparados:**
  * `SubscriptionPeriodExtendedEvent`: Se dispara siempre que el registro del pago sea exitoso para notificar la nueva vigencia financiera.
  * `SubscriptionReactivatedEvent`: Se dispara **únicamente** si el estado previo de la comunidad era `SUSPENDED` y la nueva `paidThroughDate` resultante es estrictamente mayor a la fecha actual (`NOW`).

* **Reglas de Negocio (Invariantes - Transaccionales):**
  * *RN-SUS-04.1 - Autorización Estricta:* Verificar el rol global de `SystemAdmin`.
  * *RN-SUS-04.2 - Prevención de Pagos Duplicados:* Validar la unicidad del `referenceNumber` en el repositorio de pagos.
  * *RN-SUS-04.3 - Cálculo Aritmético de Vigencia:* El sistema debe dividir el `amount` recibido entre el `basePrice` para determinar la cantidad de ciclos exactos pagados. A continuación, el sistema leerá el `billingCycle` del contrato para traducir esos ciclos a tiempo (ej. `MONTHLY` = 1 mes).
  Este tiempo resultante se sumará estrictamente a la `paidThroughDate` actual de la comunidad. **Nota para desarrollo:** El sistema jamás debe sumar este tiempo a la fecha de ejecución actual (`NOW()` o fecha de depósito), garantizando así que los pagos adelantados no roben días al cliente, y los pagos atrasados no perdonen deuda histórica. El nuevo valor resultante (`newPaidThroughDate`) debe persistirse en el perfil de la comunidad al finalizar la transacción.
  * *RN-SUS-04.4 - Evaluación de Transición de Estado:* Tras la actualización de la fecha, el sistema debe reevaluar el estado operativo:
    * Si `newPaidThroughDate > NOW()` y el estado es `SUSPENDED`: El estado muta a `ACTIVE` (se levanta el castigo).
    * Si `newPaidThroughDate > NOW()` y el estado es `PENDING_START`: El estado permanece en `PENDING_START`. El pago adelanta la vigencia financiera, pero la activación operativa debe esperar a que se cumpla la fecha del contrato (responsabilidad del UC-SUS-12).
    * Si `newPaidThroughDate <= NOW()`: El estado debe permanecer en `SUSPENDED` (indicando que la deuda parcial fue cubierta pero aún falta liquidez para el mes en curso).
  * *RN-SUS-04.5 - Tope de Límite Contractual:* El sistema rechazará la transacción si la `newPaidThroughDate` excede la `contractEndDate` (Vigencia Legal del Contrato).
  * *RN-SUS-04.6 - Restricción de Pagos Fraccionados (MVP):* Dado que el sistema en su primera fase no gestiona "Billeteras" ni "Saldos a favor", el sistema debe validar estrictamente que el monto recibido sea un múltiplo exacto del precio base (`amount % basePrice == 0`). Si el monto contiene un remanente o es menor a un ciclo completo, la operación será rechazada con un error (`ej. HTTP 422 Unprocessable Entity`) indicando: "El monto debe ser un múltiplo exacto de la tarifa. No se aceptan abonos parciales ni pagos con saldo a favor en esta versión".

* **Consideraciones / Edge Cases:**
  * *Fallo Transaccional (Rollback Estricto):* Si por cualquier motivo técnico o de negocio (falla de base de datos, caída de red, o violación de la RN-SUS-04.5) la operación no puede completarse en su totalidad, el sistema ejecutará un *rollback* absoluto. No se guardará el ingreso de dinero, ni se alterarán las fechas o estados. El operador recibirá un mensaje de error y deberá capturar el pago nuevamente desde cero una vez resuelto el incidente.
  * *Pagos Fraccionados (Fase 2):* Para el MVP se asumen pagos exactos (múltiplos del `basePrice`). Cualquier remanente fraccionado (ej. 1.5 meses) se documenta como deuda técnica para ser manejado en el futuro mediante un "Saldo a Favor", para evitar el desfase de los días de corte al sumar días sueltos.

* **Referencias Arquitectonicas:**
  * [Diagrama de flujo](./flujoRegistrarPagoManual.puml)
  * [Guía de implementación](./guiaImplementacionPagoManual.md)


### UC-SUS-05: Reactivación Manual / Extensión de Gracia (`GrantGracePeriodUseCase`)

* **Actor:** `SystemAdmin` (Personal interno de Axolote Solutions).
* **Descripción:** Flujo administrativo de emergencia (Comando) que permite al equipo de soporte restablecer el servicio de un fraccionamiento suspendido otorgando un periodo de gracia temporal. Este caso de uso separa la continuidad operativa de la conciliación contable: no registra ingresos financieros, simplemente extiende la fecha de vigencia (`paidThroughDate`) a corto plazo para dar tiempo a que un pago bancario (SPEI) en tránsito se refleje.
* **Comando de Entrada:** `GrantGracePeriodCommand`
  * `systemAdminId` (ID del empleado de Axolote, para auditoría estricta).
  * `communityId` (Identificador del fraccionamiento).
  * `graceHours` (Cantidad de horas de gracia a otorgar, ej. 48 o 72).
  * `reason` (Justificación obligatoria, ej. "Comprobante SPEI recibido por WhatsApp, en espera de conciliación bancaria").
* **Salida Esperada:** Confirmación de extensión de gracia y mutación del estado operativo a `ACTIVE`.
* **Eventos Disparados:**
  * `GracePeriodGrantedEvent` (Registra en auditoría la acción excepcional para futuras revisiones).
  * `SubscriptionReactivatedEvent` (Interceptado por el contexto de Accesos para levantar el Blackout y permitir que las plumas vuelvan a funcionar de inmediato).
  * `SubscriptionStatusChangedEvent` (Disparado por la mutación del estado de `SUSPENDED` a `ACTIVE`. Interceptado por el Contexto de Notificaciones para ejecutar el **UC-NOT-07**, inyectando el `reason` original del comando para informar al administrador del fraccionamiento).

* **Reglas de Negocio (Invariantes):**
  * *RN-SUS-05.1 - Auditoría y Justificación:* La operación será rechazada si el comando no es invocado por un `SystemAdmin`. El campo `reason` es estrictamente obligatorio y debe tener una longitud mínima (ej. 15 caracteres) para garantizar una bitácora descriptiva.
  * *RN-SUS-05.2 - Límite de Extensión Seguro:* Para evitar abusos operativos o "regalar" meses de servicio, el parámetro `graceHours` no podrá superar un límite duro configurado a nivel de sistema (ej. máximo **72 horas**).
  * *RN-SUS-05.3 - Desacoplamiento Financiero Absoluto:* A diferencia del `UC-SUS-04`, este caso de uso **no interactúa con el historial de pagos**. No se generará ningún recibo, ni se alterará el saldo contable ni la facturación de la comunidad. Su única responsabilidad es redefinir temporalmente la `paidThroughDate` asignándole el valor de `NOW() + graceHours`.
  * *RN-SUS-05.4 - Transición Automática y Reversibilidad:* Si el fraccionamiento se encontraba en estado `SUSPENDED`, al extenderse la fecha hacia el futuro, el estado mutará automáticamente a `ACTIVE`. Sin embargo, si transcurridas las horas de gracia no se ejecuta el **UC-SUS-04** para registrar el dinero real, el cron job diario (`UC-SUS-11`) lo volverá a suspender implacablemente.
  * *RN-SUS-05.5 - Restricción por Estado Inactivo:* Este comando **no puede** ser ejecutado sobre fraccionamientos que han sido dados de baja definitivamente (estado `INACTIVE`). Solo aplica para clientes con contrato vigente pero en morosidad (`SUSPENDED`).

* **Consideraciones / Edge Cases:**
  * *Abuso de Gracia:* Aunque el límite es de 72 horas por ejecución, el equipo de producto podría considerar en el futuro implementar una regla adicional que limite cuántas veces al mes se puede ejecutar este comando sobre una misma comunidad (ej. máximo 1 vez por mes de facturación) para forzar al cliente a mejorar sus prácticas de pago.


### UC-SUS-09: Consultar Mi Estado de Facturación (`GetMyBillingProfileQuery`)

* **Actor:** `CommunityAdmin` (Administrador del fraccionamiento operando desde su portal web o app móvil).
* **Descripción:** Flujo de lectura restringida (Query). Alimenta la sección de "Facturación / Suscripción" en el panel de control del cliente. Devuelve el estado actual del servicio, el ciclo de cobro, el método de pago configurado y el historial de transacciones, permitiendo al cliente tomar decisiones operativas de autoservicio (como revisar su vigencia o generar su referencia para pago manual).
* **Consulta de Entrada:** `GetMyBillingProfileQuery`
  * `communityAdminId` (ID del usuario autenticado que realiza la petición).
  * `communityId` (Identificador del fraccionamiento que se desea consultar).
* **Salida Esperada:** Objeto `ClientBillingProfileDTO` estructurado para la vista del usuario:
  * `Subscription`: Datos del contrato (`status` actual, `basePrice`, `billingCycle`, `paidThroughDate`).
  * `PaymentMethod`: Objeto enmascarado (`brand`, `last4`, `expMonth`, `expYear`) o `null` si pagan manualmente (Fase MVP).
  * `BillingHistory`: Lista paginada o limitada de las últimas facturas/recibos generados.
* **Eventos Disparados:** Ninguno. (Por ser una operación de solo lectura, no muta el estado ni emite eventos de dominio).

* **Reglas de Consulta (Filtros y Autorización):**
  * *RQ-SUS-09.1 - Aislamiento Estricto de Datos (Aislamiento de Comunidad):* El sistema debe validar criptográficamente que el `communityAdminId` tiene permisos de administración explícitos sobre el `communityId` solicitado. Si intenta inyectar el ID de otro fraccionamiento, la petición será rechazada con un `HTTP 403 Forbidden`.
  * *RQ-SUS-09.2 - Cumplimiento PCI Visual:* En estricto apego al ADR-013, el sistema jamás devolverá datos bancarios sensibles completos. La capa de presentación recibirá únicamente los metadatos ofuscados devueltos por el procesador de pagos externo (cuando aplique).
  * *RQ-SUS-09.3 - Ocultamiento de Notas Internas:* A diferencia del `UC-SUS-03` (usado por el equipo global), esta consulta omitirá cualquier campo de "comentarios internos" o "notas de soporte" dejadas por el equipo de Axolote Solutions durante registros de pagos manuales.

* **Consideraciones / Edge Cases (Técnicas):**
  * *Indicador de Acción Requerida (Call to Action):* Para facilitar el desarrollo del frontend, el `ClientBillingProfileDTO` puede incluir una bandera calculada al vuelo (ej. `requiresAction: boolean`). Si el estado es `SUSPENDED` o la vigencia está próxima a expirar, el backend envía esta bandera en `true` para que el frontend resalte en rojo la sección y habilite el botón que invita al cliente a generar su ficha de depósito ejecutando el **UC-SUS-13**.


### UC-SUS-10: Actualizar Datos Administrativos de Fraccionamiento (`UpdateCommunityProfileUseCase`)

* **Actor:** `SystemAdmin` (Personal interno de Axolote Solutions operando desde el panel global).
* **Descripción:** Flujo de mantenimiento (Comando) que permite corregir errores tipográficos o actualizar la información de contacto y ubicación de un fraccionamiento existente. Este caso de uso es estrictamente para metadatos demográficos y **no altera** las condiciones comerciales del contrato (precio o capacidad) ni la identidad del administrador local.
* **Comando de Entrada:** `UpdateCommunityProfileCommand`
  * `systemAdminId` (ID del empleado de Axolote que ejecuta la corrección, para auditoría).
  * `communityId` (Identificador único e inmutable del fraccionamiento).
  * `communityName` (Nombre corregido).
  * `address` (Dirección física corregida).
* **Salida Esperada:** Confirmación de actualización exitosa.
* **Eventos Disparados:**
  * `CommunityProfileUpdatedEvent` (Interceptado por el Contexto de Comunidad para sincronizar el nombre corregido en la interfaz de los residentes).

* **Reglas de Negocio (Invariantes):**
  * *RN-SUS-10.1 - Autorización Estricta:* El sistema rechazará la petición con un `HTTP 403 Forbidden` si el usuario no tiene el rol global de `SystemAdmin`.
  * *RN-SUS-10.2 - Protección de Inmutabilidad Contractual:* Este caso de uso no aceptará ni procesará campos relacionados con la facturación (`totalCapacity`, `basePrice`, `billingCycle`). Si se requiere modificar el contrato comercial por un error de captura, se deberá utilizar un flujo distinto o requerir la cancelación y reaprovisionamiento, garantizando la integridad financiera.
  * *RN-SUS-10.3 - Prevención de Re-aprovisionamiento:* Al ser una simple mutación de texto, este comando no interactúa con el Contexto IAM (no altera correos ni contraseñas) ni con el motor de base de datos Multi-comunidad (no renombra esquemas).

#### Política Operativa de Corrección de Contratos (Anexo al UC-SUS-10)

**Contexto:** El `UC-SUS-10` está restringido exclusivamente a metadatos tipográficos (nombre y dirección) para proteger la inmutabilidad del historial financiero.

**Procedimiento ante Errores de Captura Comercial:**
Si el equipo de ventas o administración comete un error al capturar las condiciones comerciales base (ej. registrar un `basePrice` de $200 en lugar de $2,000, o una `totalCapacity` equivocada) durante la ejecución del **UC-SUS-01**, el sistema **no proveerá** una interfaz ni un endpoint para editar dichos valores. 

La resolución estándar y obligatoria será:
1. **Inactivación:** Modificar manualmente el estado de la suscripción errónea a `INACTIVE` (cancelada) en la base de datos o a través de herramientas de soporte nivel 3, añadiendo una nota de auditoría ("Cancelado por error de captura en contrato").
2. **Re-aprovisionamiento:** Ejecutar nuevamente el flujo limpio del **UC-SUS-01: Aprovisionar Nuevo Fraccionamiento** con los datos financieros correctos, generando un nuevo `communityId` y asegurando que la primera factura y vigencia nazcan matemáticamente correctas.

### UC-SUS-11: Suspender Suscripción por Morosidad (`SuspendCommunitySubscriptionUseCase`)

* **Actor:** `System` (Ejecutado automáticamente por un proceso en segundo plano / Cron Job diario) o `SystemAdmin` (Ejecución manual preventiva).
* **Descripción:** Flujo transaccional y de orquestación (Comando) que impone el estado de "Blackout Comercial" a un fraccionamiento. El sistema evalúa si la vigencia del servicio (`paidThroughDate`) ha expirado de forma definitiva. Al ejecutarse, muta el estado de la suscripción a `SUSPENDED` y propaga la orden de bloqueo a los demás microservicios de AxolPass.
* **Comando de Entrada:** `SuspendSubscriptionCommand`
  * `performerId` (ID del `System` o del empleado de Axolote, para auditoría).
  * `communityId` (Identificador del fraccionamiento).
  * `reason` (Motivo de la suspensión, ej. "Vigencia expirada" o "Incumplimiento de contrato").
* **Salida Esperada:** Actualización del estado operativo en la base de datos local a `SUSPENDED`.
* **Eventos Disparados:**
  * `SubscriptionSuspendedEvent` (Este es el evento crítico que aplica el "castigo" comercial):
    * **Interceptado por Accesos:** Activa el "Modo de Bloqueo". Las plumas vehiculares no se abren con la App, se deshabilita la validación de nuevos códigos QR y los residentes ven una pantalla de "Servicio Suspendido" en su aplicación móvil.
    * **Interceptado por Comunidad:** Bloquea las capacidades de escritura del panel del administrador local. No pueden registrar nuevas casas, generar reportes ni crear nuevos residentes hasta que se liquide el adeudo.
  * `SubscriptionStatusChangedEvent` **(NUEVO):** Disparado por la mutación del estado de `ACTIVE` a `SUSPENDED`. Interceptado por el Contexto de Notificaciones para ejecutar el **UC-NOT-07**, inyectando el `reason` original del comando para despachar la alerta de contingencia o cobranza al administrador local.

* **Reglas de Negocio (Invariantes):**
  * *RN-SUS-11.1 - Idempotencia de Bloqueo:* Si el estado del fraccionamiento ya es `SUSPENDED` o `INACTIVE`, el sistema ignorará el comando y no disparará eventos redundantes, evitando sobrecargar el bus de mensajes.
  * *RN-SUS-11.2 - Tolerancia Cero (Evaluación de Vigencia):* La suspensión se ejecutará en el instante en que el proceso trabajador determine que la fecha actual es mayor a la `paidThroughDate`. El sistema no requiere conocer el estado de la tarjeta de crédito externa; su única fuente de verdad es la expiración del periodo pagado.
  * *RN-SUS-11.3 - Inmutabilidad Contable:* La suspensión del servicio prohíbe el acceso operativo, pero **no elimina** la deuda histórica ni borra el método de pago (`paymentToken`) previamente guardado. El perfil del cliente se congela a la espera del **UC-SUS-05 (Reactivar Suscripción)** o el pago automático.

* **Consideraciones / Edge Cases (Técnicas):**
  * *El Motor de Disparo (Cron Job):* Para evitar pre-optimizaciones y reacciones prematuras ante fallos de red bancarios (soft-declines), el sistema no escuchará webhooks de pagos fallidos para desencadenar la suspensión. En su lugar, un proceso trabajador (*Worker*) ejecutará una consulta masiva todos los días (ej. a las 00:01 AM) buscando los registros `ACTIVE` donde `paidThroughDate < NOW()` y despachará este comando para cada uno, permitiendo que la pasarela externa gestione sus propios reintentos de cobro de forma transparente.
  * *Degradación Elegante (Graceful Degradation):* Es crucial que el equipo de Accesos y Aplicación Móvil, al escuchar el `SubscriptionSuspendedEvent`, diseñen pantallas claras que informen al residente que el bloqueo se debe a un tema administrativo de su fraccionamiento, redirigiendo las quejas hacia su administrador local en lugar de hacia el equipo de soporte de Axolote Solutions.


### UC-SUS-12: Activar Comunidad Programada (`ActivateScheduledCommunityUseCase`)

* **Actor:** `System` (Ejecutado automáticamente por un proceso en segundo plano / Cron Job diario).
* **Descripción:** Flujo transaccional de transición de estado. Se encarga exclusivamente de buscar aquellas comunidades que nacieron con efectividad diferida y cuyo día de inicio ha llegado, transicionando su estado de `PENDING_START` a `ACTIVE` y habilitando su operatividad en el ecosistema.
* **Comando de Entrada:** `ActivateScheduledCommunityCommand`
  * `communityId` (Identificador de la comunidad a activar).
* **Salida Esperada:** Actualización del estado en la base de datos local a `ACTIVE`.
* **Eventos Disparados:**
  * `CommunityOperationsStartedEvent`: (Evento crucial que notifica al Contexto de Accesos y de Comunidad que este fraccionamiento ya está legal y financieramente autorizado para comenzar a operar las casetas y utilizar el panel web).
  * `SubscriptionStatusChangedEvent` **(NUEVO):** Disparado por la mutación del estado de `PENDING_START` a `ACTIVE`. Interceptado por el Contexto de Notificaciones para ejecutar el **UC-NOT-07**, informando al administrador local sobre la activación y el inicio oficial del servicio.

* **Reglas de Negocio (Invariantes):**
  * *RN-SUS-12.1 - Idempotencia Estricta:* El sistema ignorará silenciosamente el comando si el estado de la suscripción no es exactamente `PENDING_START`. Esto evita que un error de infraestructura reactive clientes cancelados (`INACTIVE`) o morosos (`SUSPENDED`).
  * *RN-SUS-12.2 - Condición Temporal:* La transición de estado solo se ejecutará si la `contractStartDate` es menor o igual a la fecha actual del servidor.
  * *RN-SUS-12.3 - Independencia Financiera:* A diferencia del `UC-SUS-05 (Reactivar Suscripción)`, este caso de uso asume que el primer cobro ya fue resuelto durante el cierre de ventas (`UC-SUS-01`), por lo que no verifica saldos ni transacciones previas para dar acceso.

* **Consideraciones / Edge Cases (Técnicas):**
  * *Segregación de Infraestructura:* Para mantener la arquitectura limpia (SRP), el *Worker* nocturno ejecutará dos consultas a base de datos separadas. Primero buscará a los que debe activar y despachará el `ActivateScheduledCommunityCommand` (hacia este UC-SUS-12). Luego buscará a los que debe suspender y despachará el `SuspendSubscriptionCommand` (hacia el UC-SUS-11).


### UC-SUS-13: Consultar Referencia de Pago Manual (`GetManualPaymentReferenceQuery`)

* **Actor:** `CommunityAdmin` (Administrador del fraccionamiento operando desde su portal web).
* **Descripción:** Flujo de lectura (Query) de acción inmediata. Usualmente invocado desde el panel de estado de cuenta (**UC-SUS-09**) cuando el cliente decide realizar un pago. Genera las instrucciones exactas para liquidar la suscripción mediante transferencia bancaria (SPEI) o depósito físico. El sistema calcula el monto a pagar basándose en el precio base y la preferencia de ciclo de facturación (`billingCycle`) actual.
* **Consulta de Entrada:** `GetManualPaymentReferenceQuery`
  * `communityAdminId` (ID del usuario autenticado, para autorización).
  * `communityId` (Identificador del fraccionamiento).
* **Salida Esperada:** Un objeto `PaymentInstructionsDTO` que contiene:
  * `amountDue` (El monto exacto a depositar).
  * `concept` (Concepto de pago alfanumérico único para facilitar la conciliación, ej. `AXP-BOSQUES-26`).
  * `bankDetails` (Objeto con el banco, CLABE interbancaria, y titular de la cuenta de Axolote Solutions).
  * `dueDate` (Fecha límite de pago antes de la suspensión).
* **Eventos Disparados:** Ninguno. (Operación de solo lectura).

* **Reglas de Consulta:**
  * *RQ-SUS-13.1 - Aislamiento de Datos:* El sistema debe validar que el `communityAdminId` pertenece al `communityId` solicitado (`HTTP 403 Forbidden` en caso contrario).
  * *RQ-SUS-13.2 - Cálculo Directo de Monto:* El sistema leerá el `basePrice` directamente del contrato de la suscripción. Dado que `basePrice` ya representa el monto exacto a cobrar por el ciclo de facturación vigente (sea `MONTHLY` o `ANNUAL`), el `amountDue` será exactamente igual a este valor. No se realizarán multiplicaciones adicionales.
  * *RQ-SUS-13.3 - Generación de Concepto Rastreable:* El backend debe asegurar que el campo `concept` sea lo suficientemente descriptivo y único para que, cuando el equipo de Axolote reciba el estado de cuenta del banco, puedan identificar rápidamente de quién es el dinero y ejecutar el **UC-SUS-04: Registrar Pago Manual**.


### UC-SUS-14: Cancelar / Dar de Baja Fraccionamiento (`CancelSubscriptionUseCase`)

* **Actor:** `SystemAdmin` (Personal interno de Axolote Solutions).
* **Descripción:** Flujo transaccional crítico (Comando) que da de baja definitiva a un cliente B2B del ecosistema AxolPass, cerrando su ciclo de vida comercial. El sistema aplica un borrado lógico (Soft Delete) cambiando el estado de la suscripción a `INACTIVE`. Este estado difiere de `SUSPENDED` (que es temporal y por morosidad); `INACTIVE` indica que ya no hay relación comercial vigente. Este proceso bloquea cualquier operación futura de forma irreversible, preservando de manera inmutable el historial financiero y operativo.
* **Comando de Entrada:** `CancelSubscriptionCommand`
  * `systemAdminId` (ID del empleado que ejecuta la acción, para auditoría estricta).
  * `communityId` (Identificador del fraccionamiento a dar de baja).
  * `cancellationType` (Enumerador estricto. Valores permitidos: `DATA_ENTRY_ERROR`, `CONTRACT_NOT_RENEWED`).
  * `notes` (Justificación en texto libre, obligatoria para detallar el contexto de la cancelación).
* **Salida Esperada:** Confirmación de baja exitosa y mutación del estado a `INACTIVE`.
* **Eventos Disparados:**
  * `SubscriptionCanceledEvent` (Interceptado por todos los demás contextos: *Accesos* bloquea el funcionamiento de casetas; *Comunidad* congela el panel administrativo; *IAM* **invalida obligatoriamente** todas las sesiones activas y tokens de acceso de los usuarios pertenecientes a dicho fraccionamiento).
  * `SubscriptionStatusChangedEvent` **(NUEVO):** Disparado por la mutación del estado a `INACTIVE`. Interceptado por el Contexto de Notificaciones para ejecutar el **UC-NOT-07**, enviando el aviso de terminación de contrato y alertando sobre el cierre operativo definitivo del fraccionamiento.

* **Reglas de Negocio (Invariantes):**
  * *RN-SUS-14.1 - Tipificación y Auditoría:* La operación exige el rol de `SystemAdmin`. El sistema no aceptará cancelaciones arbitrarias; el `cancellationType` debe estar explícitamente definido y el campo `notes` debe tener una longitud mínima (ej. 15 caracteres) para garantizar una justificación auditable.
  * *RN-SUS-14.2 - Reglas de Cancelación Deterministas:* Se elimina el uso de banderas de "override" manuales. El sistema evaluará el tipo de cancelación de forma estricta:
    * Si `cancellationType == CONTRACT_NOT_RENEWED`: El sistema **rechazará** la petición si la fecha actual es menor a la `contractEndDate`. Un contrato no puede cancelarse por este motivo si legalmente sigue vigente.
    * Si `cancellationType == DATA_ENTRY_ERROR`: El sistema **rechazará** la petición si han pasado más de **7 días** desde la fecha de aprovisionamiento del fraccionamiento. Esto otorga una ventana suficiente para corregir errores de ventas (`UC-SUS-01`), pero impide que se borren clientes establecidos por accidente o mala fe.
  * *RN-SUS-14.3 - Borrado Lógico Absoluto:* El sistema **jamás ejecutará un `DELETE`** en las tablas de la base de datos SQL. Solo se mutará el atributo `SubscriptionStatus` a `INACTIVE`. Todos los datos asociados (casas, invitaciones, pagos previos) permanecerán intactos, garantizando la integridad referencial y contable.
  * *RN-SUS-14.4 - Desvinculación Financiera:* Si el fraccionamiento tuviera un método de pago tokenizado (Fase 2, vía tarjeta externa), el sistema deberá invocar asíncronamente a la pasarela de pagos externa para cancelar la suscripción automática allá también, evitando cobros fantasmas a un cliente dado de baja.
  * *RN-SUS-14.5 - Idempotencia:* Si el fraccionamiento ya se encuentra en estado `INACTIVE`, el sistema retornará un éxito inmediato sin emitir eventos duplicados ni alterar la auditoría original.
