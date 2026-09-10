# **Aggregate Candidates – AxolPass (Etapa 2.5)**  
Modelos de agregados conceptuales por bounded context.

---

# **1. Contexto de Suscripción y Facturación**  
### **Agregado: Contrato Comercial (Subscription)**

#### **Raíz del Agregado**
- **Subscription** (Suscripción / Contrato B2B)

#### **Límites del Agregado**
Agrupa las condiciones comerciales del contrato, incluyendo:
- capacidad total contratada (`totalCapacity`)
- tarifa base (`basePrice`)
- ciclo de cobro
- fechas de vigencia
- perfil financiero (`FinancialProfile`)
- historial de pagos (`PaymentRecord`)
- periodos de gracia (`GracePeriod`)
- estado comercial
- motivo de cancelación (`CancellationType`)

#### **Invariantes que Protege**
- `totalCapacity` es inmutable una vez aprovisionado el contrato.  
- `basePrice` no puede modificarse retroactivamente.  
- La fecha de inicio de operaciones no puede ser retroactiva.  
- `paidThroughDate` nunca puede retroceder tras registrar un pago.  
- Un pago debe ser un múltiplo exacto de la tarifa base.  
- La cancelación no puede alterar el historial financiero.

#### **Reglas que Encapsula**
- Calcula la nueva `paidThroughDate` a partir de pagos reconocidos.  
- Determina si la vigencia comercial ha expirado.  
- Controla la transición hacia morosidad (CommercialBlackout).  
- Administra periodos de gracia.  
- Centraliza reglas de cancelación preservando historial.

#### **Relaciones con Otros Agregados**
- Fuente de verdad de condiciones comerciales del fraccionamiento.  
- Limita el crecimiento de **Community**.  
- Su estado comercial origina **CommercialBlackout**.  
- No modifica directamente otros agregados; comunica cambios de estado.

---

# **2. Contexto de Gestión de Comunidad**

Este contexto contiene **dos agregados independientes**.

---

## **Agregado A: Recinto (Community)**

#### **Raíz del Agregado**
- **Community** (Fraccionamiento / Recinto)

#### **Límites del Agregado**
Agrupa:
- configuración operativa global (`CommunitySettings`)
- zona horaria
- límites operativos
- capacidad global de estacionamiento
- límites de invitaciones
- catálogo de modelos arquitectónicos (`HouseModel`)

#### **Invariantes que Protege**
- Capacidades operativas no pueden ser negativas.  
- Límites deben ser enteros positivos.  
- Zona horaria debe ser IANA válida.  
- Configuración global debe ser consistente.  
- Aforo vehicular es una única capacidad global (MVP).

#### **Reglas que Encapsula**
- Establece reglas operativas maestras.  
- Valida configuraciones globales antes de operar.  
- Define límites usados por procesos de acceso y aforo.

#### **Relaciones con Otros Agregados**
- Provee configuración temporal y operativa a **Control de Accesos**.  
- Provee límite máximo a **ParkingQuota**.  
- Es el ámbito lógico de las unidades **House**.  
- Su capacidad estructural está limitada por **Subscription**.

---

## **Agregado B: Unidad Privativa (House)**

#### **Raíz del Agregado**
- **House** (Casa / Lote / Unidad Privativa)

#### **Límites del Agregado**
Agrupa:
- identificación espacial  
- estado operativo (`OperationalStatus`)  
- relaciones de residencia (`Tenancy`)  
- residente principal y secundarios  

#### **Invariantes que Protege**
- Identificación espacial debe ser única.  
- Solo un residente principal activo.  
- Relaciones de residencia deben respetar límites de capacidad.  
- Una casa suspendida no puede ejercer privilegios operativos.  
- Unicidad espacial requiere coordinación fuera de una sola instancia.

#### **Reglas que Encapsula**
- Administra ciclo de vida de vínculos de residencia.  
- Eliminación del residente principal elimina dependientes.  
- Suspensión congela privilegios operativos.  
- Cambios de estado pueden originar cancelación de invitaciones futuras.

#### **Relaciones con Otros Agregados**
- Origen residencial para **Invitation**.  
- Usa `globalUserId` sin duplicar datos personales.  
- Suspensiones coordinan cancelaciones mediante casos de uso o eventos.

---

# **3. Contexto de Seguridad e Identidad (IAM)**  
### **Agregado: Identidad de Plataforma (Account)**

#### **Raíz del Agregado**
- **Account**, identificada por `globalUserId` inmutable.

#### **Límites del Agregado**
Agrupa:
- identidad externa del proveedor  
- datos de contacto validados  
- estado de cuenta (`AccountStatus`)  
- roles de seguridad  
- ámbitos de autorización (`CommunityScope`)

#### **Invariantes que Protege**
- Identidad externa debe mapear inequívocamente a identidad interna.  
- Un método de contacto único no puede representar múltiples usuarios.  
- Roles operativos solo válidos dentro de comunidades asignadas.  
- AxolPass no almacena contraseñas.  
- Otros contextos no deben duplicar datos personales.

#### **Reglas que Encapsula**
- Administra ciclo de vida de la cuenta.  
- Administra roles y ámbitos de autorización.  
- Centraliza información personal transversal.  
- Aplica políticas de sesión diferenciadas por rol.

#### **Relaciones con Otros Agregados**
- Exporta `globalUserId` como referencia estable.  
- **House** usa `globalUserId` en `Tenancy`.  
- Comunidad y Accesos usan identidad como referencia.  
- IAM autentica y autoriza transversalmente.

---

# **4. Contexto de Control de Accesos**

Este contexto contiene **tres agregados**.

---

## **Agregado A: Pase de Acceso (Invitation)**

#### **Raíz del Agregado**
- **Invitation**

#### **Límites del Agregado**
Agrupa:
- información del visitante  
- perfil y tipo de visita  
- fecha/hora programadas  
- credencial (`AccessToken`)  
- estado del pase  
- instantes reales de entrada/salida

#### **Invariantes que Protege**
- No programar visitas retroactivas.  
- Máquina de estados unidireccional.  
- No registrar entrada válida si está en `IN_USE`.  
- No cancelar si el visitante ya ingresó.  
- Solo válida dentro de su ventana temporal.  
- Requiere que la casa origen tenga capacidad operativa.

#### **Reglas que Encapsula**
- Gobierna transiciones `PENDING → IN_USE → COMPLETED`.  
- Previene reutilización (anti-passback).  
- Controla condiciones de cancelación.  
- Determina consumo de capacidad vehicular.  
- Cierre lógico por `AUTO_COMPLETED`.

#### **Relaciones con Otros Agregados**
- Asociada a **House**.  
- Usa reglas operativas de **Community**.  
- Modifica **ParkingQuota**.  
- Genera hechos para **Notificaciones**.  
- Restricciones por `CommercialBlackout` se aplican antes de operar.

---

## **Agregado B: Concurrencia Espacial (ParkingQuota)**

#### **Raíz del Agregado**
- **ParkingQuota**

#### **Límites del Agregado**
Agrupa:
- capacidad máxima  
- ocupación actual  
- disponibilidad resultante

#### **Invariantes que Protege**
- Solo accesos vehiculares modifican ocupación.  
- Peatones/transitorios no consumen capacidad.  
- Consistencia bajo concurrencia.  
- Capacidad máxima no puede ser negativa.

#### **Reglas que Encapsula**
- Consume capacidad al registrar ingreso vehicular.  
- Libera capacidad al registrar salida.  
- Puede tolerar disponibilidad negativa tras contingencia offline.  
- Disponibilidad negativa es desviación lógica, no física.

#### **Relaciones con Otros Agregados**
- Capacidad máxima proviene de **Community**.  
- **Invitation** determina si se consume o libera capacidad.

---

## **Agregado C: Incidente de Contingencia (EmergencyOverride)**

#### **Raíz del Agregado**
- **EmergencyOverride**

#### **Límites del Agregado**
Agrupa:
- identidad del operador  
- instante de activación  
- acción extraordinaria  
- justificación  
- estado de resolución  
- evidencia para auditoría

#### **Invariantes que Protege**
- No puede resolverse sin justificación.  
- Justificación debe ser explícita y suficiente.  
- Contingencia cerrada es evidencia histórica inmutable.

#### **Reglas que Encapsula**
- Ignora restricciones comerciales u operativas en emergencias.  
- Opera incluso bajo `CommercialBlackout`.  
- Mantiene trazabilidad aun sin sesión activa.  
- Exige regularización posterior.

#### **Relaciones con Otros Agregados**
- Actúa sobre mecanismos físicos de **Community**.  
- Se vincula mediante `globalUserId`.  
- Puede impedir cierre normal de turno si hay contingencias pendientes.

---

# **5. Contexto de Notificaciones y Alertas**  
### **Agregado: Notificación (Notification)**

#### **Raíz del Agregado**
- **Notification**

#### **Límites del Agregado**
Agrupa:
- destinatario  
- contenido  
- canal (`DeliveryChannel`)  
- prioridad  
- TTL  
- estado del envío  
- información para determinar vigencia operativa

#### **Invariantes que Protege**
- Debe tener destinatario y contenido válidos.  
- Distribución de credenciales debe respetar seguridad del canal.  
- Notificaciones obligatorias no pueden suprimirse.  
- TTL expirado invalida entrega posterior.  
- Estado del envío debe evolucionar coherentemente.

#### **Reglas que Encapsula**
- Determina si debe entregarse según naturaleza y TTL.  
- Determina canales válidos.  
- Respeta preferencias del usuario cuando aplica.  
- Ignora preferencias en alertas obligatorias.  
- TTL corto para alertas transaccionales.

#### **Relaciones con Otros Agregados**
- Reacciona a hechos de **Invitation**, **House**, **Subscription**.  
- No modifica agregados origen.  
- Consulta preferencias del usuario sin mezclarlas con ciclo de vida.  
- Entrega física pertenece a infraestructura, no al agregado.

