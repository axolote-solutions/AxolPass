# Domain Insight 001: Lotes Contiguos, Terrazas y Aforos Personalizados

* **Fecha de Registro:** 07 de Marzo de 2026
* **Estado:** Registrado para Futura Iteración (Icebox / V2.x)
* **Dominio Afectado:** `CommunityContext` (Gestión de Propiedades) y `AccessContext` (Gestión de Aforo).

## 1. El Contexto (El Caso del Mundo Real)
Durante la fase de diseño, se identificó un escenario físico común en fraccionamientos de alta plusvalía o en desarrollo: **La fusión lógica o de uso de múltiples lotes por un mismo propietario.**

**El Escenario:**
Un residente compra dos lotes contiguos (Lote A y Lote B). 
* En el Lote A, construye su residencia habitual.
* En el Lote B, construye una terraza privada, jardín o área de eventos.
* **El Problema Físico:** El Lote B, al usarse como terraza, tiene una capacidad de almacenamiento vehicular interno masivo (ej. el vecino mete 10 coches de sus invitados al jardín del Lote B para que no estorben en la calle). 
* **El Problema de Negocio:** Cuando el vecino usa su terraza, la capacidad de estacionamiento no es estática; fluctúa drásticamente dependiendo de si los coches entran a su propiedad privada o si se quedan en los cajones oficiales de visita del fraccionamiento.

## 2. El Conflicto con la Arquitectura Actual (MVP)
En la versión 1.0 (MVP), AxolPass asume que:
1.  Cada `House` hereda un número fijo de `parkingSpaces` directamente de su `HouseModel`.
2.  Las casas son entidades aisladas.

Si aplicamos este modelo rígido al vecino de la historia:
* El administrador tendría que registrar el Lote A y el Lote B como dos casas separadas.
* El vecino tendría dos cuentas (o tendría que cambiar de contexto en su app) para generar invitaciones.
* El sistema no le permitiría registrar la entrada de 10 vehículos de visita para su fiesta, porque superaría el aforo diario permitido por defecto, ignorando que el vecino los va a guardar dentro de su propiedad privada (Lote B) sin afectar el aforo global del fraccionamiento.

## 3. Posibles Soluciones Arquitectónicas (A Explorar en el Futuro)
Para resolver este caso de uso en versiones posteriores sin romper el esquema de "Modelos de Casa", se deben investigar las siguientes implementaciones:

* **Solución A: Overrides (Excepciones) a Nivel de Casa.**
  Permitir que el `CommunityAdmin` configure un "Override" o excepción directamente en la entidad `House`, sobreescribiendo el límite dictado por el `HouseModel`. 
  *Ejemplo:* `House 12` (Modelo Zafiro) -> `customParkingSpaces = 15`.

* **Solución B: Agrupación de Propiedades (Multi-Lot Tenancy).**
  Permitir que un `PrimaryResident` vincule múltiples `houseId` bajo una misma "Tenencia" o "Cuenta de Propietario", provocando que el sistema sume lógicamente los beneficios y aforos de ambas propiedades en la app móvil.

* **Solución C: Tipos de Visita Especiales (Eventos Privados).**
  Crear un flujo en el `AccessContext` llamado "Generar Evento Privado", donde el residente declare bajo protesta que sus visitas se estacionarán dentro de su propiedad, evadiendo temporalmente el contador global de `ParkingQuota` del fraccionamiento, pero bajo la estricta auditoría de la caseta.

## 4. Conclusión para el MVP
Este caso de uso queda explícitamente **fuera del alcance de la V1.0** para proteger los tiempos de lanzamiento. Para el MVP, si un vecino tiene esta situación excepcional, se manejará operativamente (fuera del sistema): el vecino deberá coordinarse directamente con el `SecurityGuard` o la administración mediante el flujo manual de contingencia (UC-ACC-06: Llegada Sorpresa).


