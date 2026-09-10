# 1. Requerimientos Funcionales (FR) - El Flujo Comercial B2B

## 1.1. Onboarding y Alta de Fraccionamientos**

* **Gestión Exclusiva:** La creación de un nuevo fraccionamiento en el sistema es responsabilidad exclusiva de Axolote Solutions a través del rol `SystemAdmin`, una vez que se firma el contrato comercial. No existirá un formulario de autoregistro en línea para condominios.
* **Captura de Datos del Cliente:** Al registrar el fraccionamiento, el sistema debe capturar: Nombre, dirección, número total de casas, cantidad de accesos físicos, datos del administrador (`CommunityAdmin`), número de contacto, fechas de pago acordadas y, de forma sugerida, la geolocalización.
* **Jerarquía de Administradores:** Un fraccionamiento solo puede tener un administrador principal (`CommunityAdmin`). Sin embargo, el sistema debe soportar que un mismo usuario (con el rol `CommunityAdmin`) pueda administrar múltiples fraccionamientos distintos.

## 1.2. Modelo de Licenciamiento y Contratos**

* **Facturación por Capacidad Total:** El contrato y el cobro mensual se definen por el número total de casas del fraccionamiento, sin permitir contrataciones parciales (ej. cobrar solo 40 casas si el condominio tiene 80).
* **Inmutabilidad de Capacidad:** Una vez configurado el número de casas en el contrato, este límite no podrá modificarse posteriormente por el cliente, asumiendo que el fraccionamiento ya no crecerá en infraestructura.
* **Vigencia:** El contrato tendrá una vigencia anual. El sistema debe soportar la renovación automática si se cuenta con pago recurrente domiciliado (tarjeta de crédito).

## 1.3. Gestión de Pagos, Morosidad (Dunning) y Suspensión**

* **Registro de Pagos:** Inicialmente, el sistema permitirá registrar el estatus de los pagos de forma manual por parte de Axolote Solutions.
* **Avisos Preventivos:** El sistema enviará notificaciones preventivas al `CommunityAdmin` y al `SystemAdmin` cuando se acerque la fecha de pago o la fecha de suspensión por falta de pago.
* **Suspensión Automática/Manual (Blackout):** Si un fraccionamiento no realiza su pago mensual, el sistema debe suspender completamente el servicio.
* **Efectos del Blackout Comercial:** Durante la suspensión por falta de pago, se bloquea la generación de nuevas invitaciones y se deniega el acceso a todos los usuarios existentes (nadie puede entrar con la app). La aplicación únicamente mostrará una advertencia general de servicio suspendido.

## 1.4. Baja y Desactivación de Clientes**

* **Borrado Lógico:** La desactivación de un fraccionamiento se manejará mediante un borrado lógico (cambio de estatus a "inactivo"), manteniendo sus datos históricos intactos.
* **Protección de Contratos Activos:** El sistema impedirá que el `SystemAdmin` elimine o desactive un fraccionamiento que mantenga un contrato activo y al corriente.
* **Justificación de Baja:** Para desactivar un fraccionamiento manualmente, el sistema exigirá al `SystemAdmin` que ingrese una justificación obligatoria.

---

# 2. Requerimientos No Funcionales (NFR) - Arquitectura y Aislamiento

## 2.1. Arquitectura Multi-Tenant (Aislamiento de Inquilinos)**

* **Aislamiento de Datos Estricto:** Dado que Axolote Solutions manejará múltiples clientes, la base de datos y la capa de servicios deben garantizar que los datos (usuarios, visitas, reportes) de un fraccionamiento sean absolutamente invisibles e inaccesibles para otro fraccionamiento.

## 2.2. Seguridad y Auditoría de Nivel Superior**

* **Trazabilidad Comercial:** Cualquier cambio de estado crítico a nivel de cuenta (alta, suspensión por pago, reactivación, desactivación y cambios de administradores) debe quedar registrado en un log de auditoría inmutable, indicando qué usuario del equipo de Axolote (`SystemAdmin`) ejecutó la acción y en qué fecha/hora.
* **Control de Acceso Axolote:** Las funciones de este contexto deben estar restringidas estrictamente al rol `SystemAdmin`, bloqueando cualquier intento de acceso a estas rutas o APIs por parte de administradores de condominios o residentes.

