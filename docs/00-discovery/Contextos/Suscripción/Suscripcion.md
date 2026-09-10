# Contexto de Suscripción y Facturación (SaaS B2B)
## 1. Requerimientos Funcionales (FR) - El Flujo Comercial y Ciclo de Vida

### 1.1. Onboarding y Alta de Fraccionamientos
* **Gestión Exclusiva (Sin Autoregistro):** La creación de un nuevo fraccionamiento en el sistema ("Tenant") es responsabilidad exclusiva de Axolote Solutions a través del rol `SystemAdmin`, una vez firmado el contrato. No existirá un portal público para que un condominio se registre por su cuenta.
* **Captura Inicial:** El registro exigirá: Nombre, dirección, administrador asignado (`CommunityAdmin`), y la **Capacidad Total** (número total de casas).
* **Jerarquía de Administradores:** El sistema soportará que un mismo usuario (`CommunityAdmin`) pueda administrar múltiples fraccionamientos distintos con una sola cuenta (ideal para empresas de administración de condominios).

### 1.2. Modelo de Licenciamiento y Contratos
* **Facturación por Capacidad Total (Inmutable):** El cobro mensual/anual se define por el número total de casas construidas/lotes del fraccionamiento. No se permiten contrataciones parciales. Una vez configurado, este límite de capacidad no podrá modificarse por el cliente.
* **Vigencia y Renovación:** Los contratos tienen vigencia anual o mensual. El sistema intentará la renovación automática al inicio de cada ciclo.

### 1.3. Gestión de Pagos e Integración
* **Transición Híbrida de Pagos:** El sistema soportará un modelo híbrido:
  * *Manual:* El `SystemAdmin` puede registrar pagos y extender la vigencia manualmente (ej. transferencias SPEI directas a la cuenta de Axolote Solutions).
  * *Automático:* El `CommunityAdmin` podrá acceder a un portal seguro en su panel para domiciliar pagos mediante tarjeta de crédito/débito usando una pasarela externa.
* **Facturación y Transparencia:** Por cada cobro exitoso, el sistema generará y enviará un comprobante de pago al `CommunityAdmin`, manteniendo un historial histórico consultable.

### 1.4. Ciclo de Vida, Morosidad (Dunning) y Suspensión
* **Avisos Preventivos y Periodo de Gracia:** El sistema notificará proactivamente al `CommunityAdmin` antes del vencimiento. Si el pago falla, se otorgará un periodo de gracia (ej. 3 días) intentando el cobro nuevamente sin afectar el servicio.
* **Suspensión Automática/Manual (Commercial Blackout):** Si se agota el periodo de gracia, o si el `SystemAdmin` lo ejecuta manualmente, el fraccionamiento cambia a estado `SUSPENDIDO`.
* **Efectos del Blackout Comercial:** El sistema bloquea inmediatamente la app de todos los residentes (no pueden generar ni reenviar pases) y de los administradores locales, mostrando una pantalla de "Servicio Suspendido". *Nota de seguridad vital:* **No se bloqueará la apertura manual (Botón de Emergencia)** en la caseta por temas de protección civil.
* **Reactivación Inmediata:** Al registrarse un pago exitoso (manual o por webhook), el sistema levanta el *Blackout* y restaura el servicio en segundos.

### 1.5. Baja y Desactivación de Clientes
* **Protección de Contratos Activos:** El sistema impedirá técnicamente que un `SystemAdmin` desactive un fraccionamiento que mantenga un saldo al corriente y contrato vigente.
* **Baja con Justificación (Borrado Lógico):** Para dar de baja un fraccionamiento, el `SystemAdmin` debe ingresar una justificación obligatoria. El sistema aplicará un borrado lógico (estado `INACTIVO`), manteniendo el historial de auditoría intacto para el futuro.

---

## 2. Requerimientos No Funcionales (NFR) - Arquitectura, Seguridad y Cumplimiento

### 2.1. Arquitectura Multi-Tenant (Aislamiento de Inquilinos)
* **Aislamiento de Datos Estricto:** La base de datos y la capa de servicios deben garantizar a nivel de sentencias SQL/ORM que los datos financieros, usuarios y configuraciones de un fraccionamiento sean criptográficamente inaccesibles para cualquier otro cliente.

### 2.2. Cumplimiento PCI-DSS (Seguridad Financiera)
* **Cero Almacenamiento Sensible:** AxolPass **jamás almacenará** números de tarjetas de crédito (PAN) ni códigos de seguridad (CVV). Toda la captura se delega a la pasarela certificada nivel 1 (ej. Stripe). El sistema de Axolote solo guardará tokens opacos (`Customer_ID`, `Subscription_ID`).

### 2.3. Tolerancia a Fallos y Concurrencia
* **Procesamiento de Webhooks Idempotente:** Los avisos de pago de la pasarela se procesarán de forma segura. Si un mismo evento llega dos veces por error de red, el sistema lo detectará mediante su ID de transacción y no duplicará vigencias ni recibos.
* **Desacoplamiento Operativo (Resiliencia):** El Contexto de Suscripciones no debe ser un punto único de falla (SPOF) para la operación física. Si este módulo o el proveedor de pagos se caen, el Contexto de Accesos y las casetas seguirán operando con normalidad basándose en la última vigencia conocida (`Heartbeat`).

### 2.4. Auditoría de Nivel Superior
* **Trazabilidad Comercial:** Toda acción que afecte la facturación o la vigencia (altas, suspensiones, reactivaciones, bajas, registros de pago manuales) debe registrarse en un log de auditoría inmutable, sellado con el ID del `SystemAdmin` y la estampa de tiempo.

