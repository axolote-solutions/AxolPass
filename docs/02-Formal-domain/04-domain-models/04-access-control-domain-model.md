# Access Control domain model

## 4. Contexto de Control de Accesos

**Propósito:** Administra el flujo táctico físico, autoriza el cruce de barreras, registra auditorías y calcula la capacidad espacial.

### **Entidades del Dominio:**
* **Invitación:** El derecho o promesa de entrada otorgada a una persona.

* **Token de Acceso:** La representación matemática/criptográfica del derecho de acceso.

* **Evento de Acceso:** El hecho consumado del cruce físico por la caseta.

### **Value Objects Conceptuales:**
* **Cuota de Estacionamiento:** El contador global unificado de lugares disponibles.

* **Tipo de Visita:** La naturaleza del cruce (vehículo, peatón, paquetería, transporte).

* **Perfil de Visitante:** Información descriptiva de quien cruza (nombre, vehículo).

### **Invariantes del Negocio:**
* La generación de una invitación falla si la casa origen está suspendida operativamente o el fraccionamiento bajo apagón comercial.

* Las llegadas sorpresa o aperturas de contingencia exigen una justificación obligatoria para la bitácora.

* Las salidas siempre tienen prioridad; un hardware desconectado debe permitir salir a los vehículos aunque no pueda sincronizar en la nube.

### **Reglas Internas del Contexto:**
* La evaluación de aforo depende del tipo de visita: entregas y peatones no descuentan lugares de estacionamiento.

* Toda visita que permanezca en curso de forma anómala (ej. más de 24 hrs) sufre una evacuación lógica forzada para liberar el aforo.

* Las aperturas desconectadas de internet asumen el riesgo a favor de la fluidez vial y delegan la resolución de conflictos para cuando regrese la red.

### **Estados y Transiciones (Invitación):**
* `PENDING` $\rightarrow$ `IN_USE` $\rightarrow$ `COMPLETED` (Flujo nominal).

* `PENDING` $\rightarrow$ `EXPIRED` / `CANCELED` / `CANCELED_BY_ADMIN` / `DELIVERY_FAILED` (Cierres prematuros).

* `IN_USE` $\rightarrow$ `AUTO_COMPLETED` (Evacuación lógica).

### **Relaciones entre Entidades:**
* La *Invitación* se materializa en un *Token de Acceso* y su uso genera *Eventos de Acceso*.

### **Límites del Agregado (Candidatos):**
* **Agregado Raíz A:** *Invitación* (Vigila la integridad de su propio ciclo de vida y uso).
* **Agregado Raíz B:** *Cuota de Estacionamiento* (Maneja la concurrencia matemática del aforo).

### **Lenguaje Ubicuo del Contexto:**
* Guest, Invitation, AccessToken, ParkingQuota, GuardConsole, KioskApp, UnscheduledVisit, EmergencyOverride.
