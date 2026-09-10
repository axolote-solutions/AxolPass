# 1. Requerimientos Funcionales (FR) - Inteligencia de Negocio y Trazabilidad

## 1.1. Visibilidad del Residente (Nivel Casa)**

* **Historial Personal y Familiar:** Los residentes (tanto principales como secundarios) deben poder consultar el historial de sus propias invitaciones y ver las invitaciones creadas por otros usuarios de su misma casa.
* **Métricas de Visitas:** El sistema mostrará a los residentes cuántas veces accedió un visitante específico durante el mes.
* **Restricción de Información Comunitaria:** Los residentes no tendrán acceso a métricas generales, uso de estacionamiento de otros vecinos, ni reportes de conflictos o saturaciones.

## 1.2. Visibilidad de la Administración Local (Nivel Fraccionamiento)**

* **Métricas Operativas:** El administrador (`CommunityAdmin`) debe tener acceso a reportes que muestren el historial de accesos por casa, las casas con mayor número de visitantes, y la frecuencia de visitas por día, semana y mes.
* **Control de Expiraciones:** El sistema debe generar registros de las invitaciones emitidas que no fueron utilizadas.
* **Herramienta de Búsqueda:** Para casos de incidentes de seguridad o vialidad, el administrador podrá buscar eventos de acceso específicos en el historial (ej. filtrando por placa o por rango de fechas).
* **Exportación de Datos:** El administrador debe poder exportar estos reportes (ej. en formato .csv o .pdf) para su análisis en asambleas o archivo.

## 1.3. Visibilidad de Axolote Solutions (Nivel Global)**

* **Panel de Control (Dashboard):** El `SystemAdmin` debe contar con una vista general de todos los fraccionamientos, indicando su estado (activos/inactivos).
* **Métricas de Adopción:** El sistema generará reportes bajo demanda que muestren los fraccionamientos con mayor uso, el promedio de uso por casa, y el total de invitaciones emitidas vs. usadas.
* **Alertas Tempranas:** El dashboard interno debe generar alertas por inactividad o baja adopción en un fraccionamiento, lo cual sirve como indicador de riesgo de cancelación (churn).

## 1.4. Trazabilidad y Auditoría de Sistema**

* **Registro Universal de Eventos:** El sistema debe auditar automáticamente cualquier evento que impacte la seguridad o configuración, incluyendo: accesos físicos, intentos fallidos, generación de invitaciones, altas/bajas de residentes, cambios de administradores y aperturas manuales por parte del guardia.
* **Detalle del Log:** Cada registro de auditoría debe capturar invariablemente: fecha y hora exacta (timestamp), tipo de evento, actor (quién lo hizo) y resultado (éxito/error).
* **Restricción de Acceso a Logs:** El acceso a la auditoría es jerárquico: el `SystemAdmin` ve todo, el `CommunityAdmin` solo ve su fraccionamiento, y los residentes solo su casa.

---

# 2. Requerimientos No Funcionales (NFR) - Privacidad y Retención

## 2.1. Protección de Datos (Data Privacy)**

* **Anonimización Administrativa:** Por respeto a la privacidad de los vecinos, el `CommunityAdmin` solo podrá ver los eventos de acceso (ej. "Ingresó un vehículo a la Casa 15"), pero no podrá ver el nombre completo del invitado en sus reportes de rutina.

## 2.2. Políticas de Retención de Datos**

* **Ciclo de Vida de los Logs:** Para cumplir con requerimientos básicos de trazabilidad sin saturar la base de datos, el sistema conservará el historial detallado de accesos e invitaciones por un periodo de 12 meses.
* **Búsqueda Indexada:** Las consultas al historial de auditoría y reportes deben estar optimizadas (mediante índices en la base de datos) para evitar que la generación de un reporte mensual degrade el rendimiento del proceso de control de accesos en las casetas.

