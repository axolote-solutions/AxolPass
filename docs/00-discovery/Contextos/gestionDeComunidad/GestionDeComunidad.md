# 1. Requerimientos Funcionales (FR) - El Flujo de Gestión Comunitaria

## 1.1. Registro y Configuración de Casas (Lotes)**

* **Responsabilidad de Configuración:** Una vez que Axolote Solutions da de alta el fraccionamiento, la configuración y gestión de las casas es responsabilidad exclusiva del administrador local (`CommunityAdmin`).
* **Métodos de Creación:** El sistema debe ofrecer dos modalidades para dar de alta las casas: generación automática por bloques continuos (ej. del 100 al 120) o alta manual individual para fraccionamientos con numeración irregular.
* **Atributos Base:** Cada casa debe mantener, como mínimo, la siguiente información: Número o nombre de casa/lote, Estado actual (activa, inactiva, suspendida) y los Residentes asociados.
* **Estados de Transición (Casas Vacías):** El sistema debe permitir que una casa exista en estado "vacía" (sin residente principal asignado) para manejar periodos de transición por venta o término de contrato de renta.

## 1.2. Gestión de Residentes (Principales y Secundarios)**

* **Asignación Principal:** Únicamente el `CommunityAdmin` tiene los privilegios para asociar a un residente principal a una casa específica.
* **Delegación a Secundarios:** El residente principal tiene la capacidad de dar de alta y eliminar a sus usuarios secundarios (familiares, roomies). La eliminación general en el sistema funciona como una inhabilitación.
* **Intervención Administrativa:** Aunque la gestión de secundarios es responsabilidad del residente principal, el `CommunityAdmin` también conserva el poder de eliminar usuarios secundarios si es necesario.
* **Proceso de Mudanza / Cambio de Residente:** Cuando cambia el residente de una casa, el administrador debe realizar un "traspaso formal" eliminando al residente anterior. Esta acción debe invalidar automáticamente a sus usuarios secundarios y todas las invitaciones pendientes asociadas a él, conservando únicamente la información para reportes históricos.

## 1.3. Reglas de Convivencia y Límites**

* **Configuración de Aforo por Casa:** El sistema debe permitir que el fraccionamiento (a través del `CommunityAdmin`) determine y configure el límite de invitaciones que puede generar cada residente.
* **Límite de Habitantes:** Existirá un límite máximo de cuentas móviles (usuarios residentes) permitidas por casa. Para proteger la infraestructura y alinearse al modelo comercial, este límite será establecido estrictamente por Axolote Solutions al momento de firmar el contrato (Aprovisionamiento), y no podrá ser alterado por el CommunityAdmin.

## 1.4. Castigos y Suspensiones Operativas**

* **Suspensión por Morosidad/Mal Uso:** El `CommunityAdmin` debe poder bloquear temporalmente el servicio a una casa específica.
* **Efectos de la Suspensión:** Cuando una casa está suspendida, el residente aún puede acceder a la aplicación móvil, pero su capacidad para generar invitaciones queda bloqueada y solo recibirá notificaciones indicando su estado de suspensión.
* **Borrado Lógico Total:** El `CommunityAdmin` puede eliminar a todos los usuarios de una casa; esto revoca todas sus invitaciones activas pero conserva el historial de accesos intacto en la base de datos.

---

# 2. Requerimientos No Funcionales (NFR) - Privacidad y Visibilidad

## 2.1. Aislamiento de Datos (Multi-tenant interno)**

* **Visibilidad de Invitaciones:** Los usuarios secundarios podrán ver las invitaciones creadas por otros usuarios que pertenezcan a su misma casa.
* **Restricción de Visibilidad y Auditoría de Excepción (Break-Glass):** Por defecto, el `CommunityAdmin` visualizará una bitácora anonimizada de los accesos (mostrando únicamente fecha, hora, tipo de visita y casa destino). Sin embargo, el sistema contará con una función de "Auditoría Forense" (Break-Glass). Al activarla para un registro específico, el administrador deberá ingresar obligatoriamente un motivo de investigación (ej. "Daños a propiedad", "Incidente de seguridad"). El sistema revelará entonces el nombre completo, identificaciones y placas del visitante, generando simultáneamente un registro inmutable en el `AuditLog` que documente quién, cuándo y por qué vulneró la privacidad de dicho registro, deslindando a Axolote Solutions y trasladando la responsabilidad legal del manejo de datos al administrador local.
* **Autonomía del Administrador Local:** El `CommunityAdmin` debe poder realizar todas las suspensiones, reactivaciones y cambios de residentes de manera autónoma, sin requerir intervención del equipo de soporte de Axolote Solutions.
