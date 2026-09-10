# Suscription domain model

## 1. Contexto de Suscripción y Facturación (SaaS B2B)

**Propósito:** Gobierna la relación comercial y contractual entre Axolote Solutions y los fraccionamientos, controlando la vigencia y las restricciones de servicio.

### **Entidades del Dominio:**
* **Suscripción:** El contrato comercial principal e inmutable.

* **Registro de Pago:** Un asiento contable validado que representa el ingreso de dinero y la extensión de vigencia.

### **Value Objects Conceptuales:**
* **Perfil Financiero:** Encapsula la aritmética financiera, las reglas de los ciclos de facturación y las tarifas matemáticas sin contaminar la entidad principal.

* **Periodo de Gracia:** Una cantidad de horas de extensión temporal otorgada administrativamente.

* **Tipo de Cancelación:** El motivo determinista (ej. error de captura, no renovación) que ampara la baja de un contrato.

### **Invariantes del Negocio:**
* La capacidad total de casas y la tarifa base son inmutables una vez que el contrato nace.

* Las fechas de inicio de operaciones jamás pueden ser retroactivas.

* El pago manual debe ser un múltiplo exacto de la tarifa, sin permitir saldos parciales en esta etapa.


### **Reglas Internas del Contexto:**
* Si la fecha actual supera la vigencia pagada del fraccionamiento, el sistema detona transversalmente un "Apagón Comercial".

* Dar de baja una suscripción aplica un borrado lógico absoluto; el historial contable se mantiene inalterable como prueba de auditoría.

### **Estados y Transiciones (Suscripción):**
* `PENDING_START` $\rightarrow$ `ACTIVE` (al llegar la fecha de inicio).

* `ACTIVE` $\leftrightarrow$ `SUSPENDED` (por morosidad o pago recibido).

* *Cualquier Estado* $\rightarrow$ `INACTIVE` (baja definitiva).

### **Relaciones entre Entidades:**
* Una *Suscripción* confía en su *Perfil Financiero* para calcular extensiones y agrupa un historial de *Registros de Pago*.

### **Límites del Agregado (Candidatos):**
* **Agregado Raíz:** *Suscripción* (Controla la consistencia de sus pagos y de su perfil financiero).

### **Lenguaje Ubicuo del Contexto:**
* Subscription, SubscriptionStatus, totalCapacity, basePrice, paidThroughDate, CommercialBlackout, FinancialProfile, GracePeriod, CancellationType, PaymentOrder.

