### 1. Actores del Negocio

Quienes ejecutan las acciones (emiten comandos) dentro del dominio:

* **SystemAdmin:** Personal de Axolote Solutions que controla los contratos B2B.


* **CommunityAdmin:** Administrador local que gobierna las reglas del fraccionamiento y gestiona a los vecinos.


* **PrimaryResident / SecondaryResident:** Vecinos que autorizan visitas a su domicilio.


* **SecurityGuard:** Personal en caseta responsable del flujo físico y las emergencias.


* **Sistema (El Reloj):** El propio negocio actuando de forma autónoma (por ejemplo, al llegar la medianoche o al vencer un contrato).



---

### 2. Estados Fundamentales del Negocio

El ciclo de vida por el que transitan las entidades principales:

* **Suscripción Comercial:** Nace como *Pending Start* o *Active*, transiciona a *Suspended* por falta de pago, y muere como *Inactive* al cancelar el contrato.


* **Unidad Privativa (Casa):** Nace *Active*, y puede transicionar a *Suspended* si la administración local la castiga por deudas o sanciones internas.


* **Invitación:** Nace *Pending*, transiciona a *In Use* cuando el visitante entra, y finaliza como *Completed* al salir. También puede morir prematuramente como *Canceled*, *Expired* (si no llegó el día de la cita), o *Delivery Failed* si fue imposible entregar el pase al visitante.



---

### 3. Recorrido del Dominio (Event Storming)

A continuación, el flujo cronológico del negocio. Leemos de izquierda a derecha: **Actor -> [Comando] -> Evento -> (Política Recreativa)**.

#### Fase A: El Nacimiento del Fraccionamiento (Onboarding Comercial)

* **SystemAdmin** ejecuta `[Aprovisionar Fraccionamiento]`.


* **Evento:** `Comunidad Aprovisionada`.


* **Política:** Cuando una comunidad es aprovisionada, se debe preparar su entorno operativo y provisionar la cuenta del administrador local.




* **CommunityAdmin** ejecuta `[Configurar Reglas de Comunidad]` (define zonas horarias y aforo).


* **Evento:** `Reglas de Comunidad Actualizadas`.


* **Política:** Cuando las reglas cambian, la caseta debe adoptar los nuevos límites operativos de inmediato.




* **CommunityAdmin** ejecuta `[Registrar Unidad Privativa]`.


* **Evento:** `Casa Registrada`.





#### Fase B: La Cotidianidad del Residente (Gestión de Visitas)

* **Residente** ejecuta `[Generar Invitación]`.


* **Evento:** `Invitación Generada`.


* **Política:** Cuando se genera una invitación, se debe distribuir el pase seguro al visitante vía WhatsApp o Correo, respetando las restricciones del fraccionamiento.




* **Residente** ejecuta `[Cancelar Invitación]`.


* **Evento:** `Invitación Cancelada`.


* **Política:** Cuando se cancela una invitación, se debe notificar inmediatamente al visitante para evitar que se traslade a la caseta en vano.





#### Fase C: La Realidad Física en Caseta (El Flujo Vehicular)

* **Visitante/Caseta** ejecuta `[Registrar Entrada]` (al presentar su pase).


* **Evento:** `Acceso Concedido` y `Espacio de Aforo Reservado`.


* **Política:** Cuando se concede un acceso, se debe notificar al residente inmediatamente indicando que su visita ha llegado.


* **Política:** Si el tipo de visita es un proveedor de transporte (Uber) o paquetería, NO se debe descontar espacio del aforo global.




* **Guardia** ejecuta `[Registrar Llegada Sorpresa]` (visita sin invitación).


* **Evento:** `Alerta de Llegada Sorpresa Registrada`.


* **Política:** Se debe enviar una alerta urgente al residente.




* **Visitante/Caseta** ejecuta `[Registrar Salida]`.


* **Evento:** `Salida Registrada` y `Espacio de Aforo Liberado`.


* **Política:** Cuando la visita se retira, se debe restituir el espacio de estacionamiento (si aplicaba) y notificar al residente de forma opcional.





#### Fase D: Contingencias y Excepciones de Seguridad

* **Guardia** ejecuta `[Ejecutar Apertura de Contingencia]` (Botón de pánico).


* **Evento:** `Apertura de Emergencia Detonada`.


* **Política:** Cuando se detona la contingencia, la barrera se abre ignorando todas las reglas, pero el guardia queda obligado a justificar la acción antes de terminar su turno.




* **Guardia** ejecuta `[Justificar Contingencia]`.


* **Evento:** `Contingencia Resuelta`.




* **Sistema** ejecuta el paso del tiempo y detona `[Invalidar Invitaciones Expiradas]`.


* **Evento:** `Invitación Expirada`.


* **Política:** Si una visita lleva más de 24 horas adentro sin registrar salida, el sistema debe forzar su cierre (evacuación lógica) para no congelar el aforo permanentemente.





#### Fase E: Castigos y Coerción (Morosidad)

* **CommunityAdmin** ejecuta `[Modificar Estado Operativo de Casa]` (Castigar a un vecino).


* **Evento:** `Estado Operativo de Casa Modificado` (a Suspendida).


* **Política:** Cuando una casa es suspendida, todas sus invitaciones futuras pendientes deben ser purgadas y canceladas inmediatamente.




* **Sistema (Reloj)** ejecuta `[Suspender Suscripción por Morosidad]`.


* **Evento:** `Suscripción Suspendida`.


* **Política:** Cuando el fraccionamiento entero cae en morosidad, se ejecuta el **Apagón Comercial**: se bloquea la generación de pases y accesos automatizados, pero por ley se deben mantener operativas las salidas y el botón de emergencia.





---

### 4. Políticas Supremas del Dominio

Durante este recorrido, el negocio respeta dos grandes reglas inquebrantables que definen a AxolPass:

1. **Prioridad de la Realidad Física:** La seguridad humana, la evacuación y las excepciones médicas siempre ganan frente a las validaciones informáticas. Una barrera siempre debe poder abrirse en contingencia, incluso si el aforo lógico queda en números negativos temporalmente.


2. **Inmutabilidad de la Auditoría:** Nada se borra. Una casa suspendida o un contrato cancelado mantienen su historial financiero y operativo intacto; los incidentes de seguridad sin justificar quedan permanentemente manchados en el registro del fraccionamiento.