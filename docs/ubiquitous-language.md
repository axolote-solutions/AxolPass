# Lenguaje Ubicuo — AxolPass

## Propósito y criterio de uso

Este glosario es la referencia canónica de términos de negocio para AxolPass. Consolida los conceptos de `01-ConceptualDomain` y `02-Formal-domain`, en particular los casos de uso, políticas y estados del MVP. Se usa el término en inglés entre acentos graves cuando identifica un concepto estable del dominio; la definición está en español.

El glosario describe el negocio, no mecanismos de implementación. No incorpora estados de conectividad, aplicaciones, APIs, colas, proveedores ni funcionalidades fuera del alcance formal del MVP. Cuando un documento anterior emplee un sinónimo, debe preferirse el término canónico indicado aquí.

---

## 1. Actores, identidad y alcance

* **`SystemAdmin`:** Personal interno de Axolote Solutions que da de alta fraccionamientos, registra pagos manuales y puede aplicar una suspensión comercial autorizada.
* **`CommunityAdmin`:** Administrador autorizado de una `Community`. Configura las reglas operativas, registra casas, asigna residentes y suspende o reactiva casas de su propia comunidad.
* **`ResidentMain`:** Residente principal de una `House`. Es el titular del vínculo de residencia activo y puede gestionar residentes secundarios e invitaciones de su casa. **Equivalencia histórica:** `PrimaryResident`.
* **`ResidentSecondary`:** Residente secundario vinculado activamente a una `House`. Puede generar invitaciones, pero no administra la casa ni delega otros residentes. **Equivalencia histórica:** `SecondaryResident`.
* **`SecurityGuard`:** Personal de seguridad autorizado para gestionar llegadas sorpresa, registrar intervenciones manuales y ejecutar aperturas de emergencia.
* **`Guest`:** Persona visitante que pretende entrar a una `Community`, ya sea mediante una `Invitation` o como `UnscheduledVisit`.
* **`User`:** Persona identificada por AxolPass que puede tener uno o más roles y un alcance de actuación definido.
* **`globalUserId` / Identidad global:** Identificador único provisto por IAM que otros contextos usan para referirse a un `User` sin duplicar su información personal.
* **`Role` / Rol operativo:** Función autorizada de un `User`, como `SystemAdmin`, `CommunityAdmin` o `SecurityGuard`, dentro del alcance que corresponda.
* **`CommunityScope`:** Límite de autoridad que establece en qué `Community` puede actuar una persona. Ningún actor puede ejercer privilegios fuera de su alcance.

---

## 2. Comunidad, casas y residencia

* **`Community`:** Fraccionamiento cliente de AxolPass y ámbito organizativo donde se agrupan casas, residentes y reglas operativas.
* **`CommunitySettings`:** Reglas operativas de una `Community`: zona horaria, límite diario de invitaciones por casa, capacidad de visitantes y política de aforo.
* **`House`:** Unidad privativa —casa, departamento o lote— perteneciente a una `Community`. Se identifica de manera única dentro de ella y puede existir sin residentes.
* **`HouseModel` / Modelo de propiedad:** Arquetipo arquitectónico con el que una `Community` clasifica sus `House`. Su comportamiento adicional no está definido por los casos de uso formales del MVP.
* **`OperationalStatus`:** Estado operativo de una `House`. En el MVP sus únicos valores son `ACTIVE` y `SUSPENDED`.
  * **`ACTIVE`:** La casa puede ejercer sus privilegios, siempre que la suscripción de su comunidad también permita operar.
  * **`SUSPENDED`:** La casa no puede generar invitaciones ni recibir nuevos accesos ordinarios. No bloquea la salida de visitas que ya ingresaron.
* **`Empty House` / Casa vacía:** Casa que no tiene vínculos de residencia activos. Describe su ocupación residencial; no es un valor de `OperationalStatus`.
* **`Tenancy` / Vínculo de residencia:** Relación histórica entre un `User` y una `House`, que determina el rol de residencia y los derechos que se derivan de él.
  * **`ACTIVO`:** El vínculo está vigente y otorga el rol de residente correspondiente.
  * **`INACTIVO`:** El vínculo terminó, pero se conserva para mantener la historia de residencia y accesos.
* **`Resident Transfer` / Traspaso formal:** Desvinculación del `ResidentMain` de una casa. Desvincula también a sus residentes secundarios y cancela las invitaciones pendientes de las personas desvinculadas; no elimina la historia.
* **`Community Capacity` / Capacidad contratada:** Máximo de `House` que puede registrar una `Community` de acuerdo con su contrato comercial.
* **`Parking Capacity` / Capacidad de visitantes:** Máximo configurado de espacios de estacionamiento destinados a visitas dentro de una `Community`.
* **`Parking Occupancy` / Ocupación de aforo:** Uso actual de la capacidad de visitantes. Aumenta con la entrada de una visita vehicular con permanencia y disminuye con su salida.
* **`Parking Policy` / Política de aforo:** Criterio con el que la comunidad trata ingresos que consumen estacionamiento. El MVP contempla las políticas estricta, de excepción y flexible; solo la política estricta está definida: rechaza el ingreso cuando se alcanzó el límite. Las otras dos requieren definición de negocio antes de operar.

---

## 3. Suscripción y operación comercial

* **`Subscription`:** Relación comercial entre Axolote Solutions y una `Community`. Define capacidad contratada, tarifa, ciclo de facturación, vigencia legal y cobertura pagada.
* **`SubscriptionStatus`:** Estado comercial de una `Subscription`. En el MVP sus valores son:
  * **`PENDING_START`:** La fecha de inicio es futura. La comunidad puede prepararse, pero no realiza operación ordinaria de accesos ni genera invitaciones.
  * **`ACTIVE`:** La comunidad tiene autorización comercial para operar.
  * **`SUSPENDED`:** La cobertura comercial venció o existe una suspensión comercial autorizada. Activa el `CommercialBlackout`.
* **`Paid Through Date` / Vigencia pagada:** Fecha hasta la cual la `Subscription` cuenta con cobertura comercial. Un pago manual la extiende desde la vigencia histórica, sin reiniciarla desde la fecha de registro.
* **`CommercialBlackout` / Apagón comercial:** Restricción comercial aplicada a toda una `Community` cuando su `Subscription` está `SUSPENDED`. Impide nuevas invitaciones, nuevos accesos ordinarios y operaciones administrativas ordinarias de escritura. Nunca impide salidas, aperturas de emergencia ni la trazabilidad necesaria para la seguridad física.
* **`Manual Payment` / Pago manual:** Pago recibido por Axolote Solutions que un `SystemAdmin` registra para extender la vigencia pagada. En el MVP cubre ciclos completos; no hay abonos parciales ni saldos a favor.
* **`Payment Reference` / Referencia de pago:** Identificador único que permite reconocer y registrar una vez un pago manual.
* **`Billing Cycle` / Ciclo de facturación:** Periodicidad comercial pactada para el cobro de una `Subscription`.
* **`Base Price` / Tarifa base:** Importe pactado por cada ciclo de facturación de la suscripción.
* **`FinancialProfile` / Perfil financiero:** Conjunto de reglas y datos comerciales que permite calcular condiciones financieras de una `Subscription`, sin alterar el contrato ni su historial.
* **`GracePeriod` / Período de gracia:** Extensión temporal administrativa asociada a la vigencia comercial. La duración y criterios de otorgamiento no están formalizados en los casos de uso del MVP.
* **`CancellationType` / Motivo de baja:** Clasificación del motivo de una cancelación comercial. La cancelación de una `Subscription` y el estado `INACTIVE` no forman parte de los ciclos de vida formales del MVP.

---

## 4. Invitaciones, visitas y accesos

* **`Invitation`:** Autorización temporal creada por un residente activo para que un `Guest` visite una `House` en un día determinado. Incluye la clasificación de visita y una hora estimada.
* **`InvitationStatus`:** Ciclo de vida de una `Invitation` en el MVP:
  * **`PENDING`:** La invitación fue generada y todavía no registra una entrada.
  * **`IN_USE`:** La visita ingresó y su salida está pendiente.
  * **`COMPLETED`:** La visita registró su salida; la invitación no permite otra entrada.
  * **`CANCELED`:** La invitación fue revocada antes de que la visita ingresara.
  * **`EXPIRED`:** El día de vigencia concluyó sin que la invitación pendiente registrara entrada.
* **`Access Pass` / Pase de acceso:** Representación entregada al visitante de una `Invitation` pendiente. En el MVP incluye QR y PIN numérico, ambos correspondientes al mismo permiso de un solo uso.
* **`Single-use Entry` / Entrada de un solo uso:** Regla por la cual una `Invitation` solo permite una entrada. Tras ella pasa a `IN_USE` y no admite una segunda entrada; conserva únicamente la posibilidad de registrar la salida.
* **`Scheduled Date` / Día programado:** Día en que una `Invitation` puede usarse para entrar, determinado según la zona horaria de la `Community`. La hora estimada no reduce esa vigencia diaria.
* **`Visit Classification` / Clasificación de visita:** Clasificación que determina si una visita consume aforo. Solo la visita vehicular con permanencia lo consume; peatones, entregas y transporte transitorio no.
* **`Access Granted` / Acceso concedido:** Resultado por el que una visita cumple las reglas aplicables y registra una entrada. Si consume aforo, origina la ocupación de un espacio.
* **`Access Denied` / Acceso rechazado:** Resultado por el que se niega una entrada. No cambia el estado de la `Invitation`, pero debe conservar evidencia del intento y de su motivo.
* **`AccessRecord` / Registro de acceso:** Concepto de Control de Accesos para registrar un hecho de entrada, salida o rechazo. Sus atributos y ciclo de vida propios no están formalizados como un modelo separado del MVP.
* **`Departure` / Salida registrada:** Hecho por el que se cierra una visita en curso. Lleva una `Invitation` de `IN_USE` a `COMPLETED` y libera aforo solo si la visita lo había consumido.
* **`UnscheduledVisit` / Llegada sorpresa:** Visita que se presenta sin una `Invitation` previa. El `SecurityGuard` la gestiona bajo las mismas restricciones ordinarias de casa, suscripción y aforo; no es una emergencia por sí misma.
* **`Manual Access` / Acceso manual:** Entrada o salida que el `SecurityGuard` registra al intervenir ante una llegada sorpresa o contingencia operativa. Debe conservar responsable, resultado y motivo cuando corresponda.

---

## 5. Contingencias, notificaciones y auditoría

* **`Manual Opening` / Apertura manual:** Intervención de un `SecurityGuard` ante una falla de lectura, barrera u otra contingencia. Requiere responsable y motivo; por sí misma no permite ignorar las reglas ordinarias de acceso.
* **`Emergency Override` / Apertura de emergencia:** Apertura excepcional para proteger vida, integridad, propiedad o vialidad. Puede eludir las restricciones ordinarias para responder de inmediato, pero exige justificación posterior y evidencia inmutable.
* **`Emergency Contingency Status`:** Ciclo de una contingencia de emergencia:
  * **`PENDING_JUSTIFICATION`:** La apertura de emergencia ocurrió y aún falta documentar su motivo.
  * **`JUSTIFIED`:** Se proporcionó una justificación válida; la contingencia queda cerrada como evidencia inmutable.
  * **`UNJUSTIFIED_SECURITY_INCIDENT`:** No se entregó justificación dentro de las condiciones establecidas y la contingencia queda clasificada como incidente de seguridad no justificado.
* **`Business Event` / Evento de negocio:** Hecho significativo expresado en pasado, como `InvitacionGenerada`, `AccesoConcedido`, `CasaSuspendida` o `SuscripcionReactivada`. Puede desencadenar políticas de negocio y debe quedar auditado cuando sea relevante.
* **`Business Policy` / Política de negocio:** Regla automática que, ante un evento y una condición, aplica una acción de negocio y sus restricciones. No es un caso de uso iniciado por una persona.
* **`Notification`:** Comunicación dirigida a un destinatario por un hecho de negocio. En el MVP, los residentes son notificados del acceso concedido o rechazado de sus visitas según su relación con la casa y la invitación.
* **`NotificationPolicy` / Política de notificación:** Regla conceptual que determina destinatario y contenido de una `Notification` a partir de un hecho de negocio. Las preferencias ordinarias de notificación no forman parte del MVP formal.
* **`Audit Record` / Registro de auditoría:** Evidencia inmutable de un hecho relevante. Incluye como mínimo momento, tipo de hecho, actor o responsable cuando corresponda, casa o comunidad asociada y resultado.
* **`Privacy-preserving View` / Consulta con privacidad preservada:** Consulta administrativa rutinaria que muestra los eventos de la comunidad sin revelar la identidad completa del visitante. La revelación excepcional de información protegida no forma parte de los casos de uso formales del MVP.

---

## 6. Distinciones obligatorias

* **`SubscriptionStatus` no es `OperationalStatus`:** La suscripción rige la relación comercial de toda la comunidad; el estado operativo rige una casa concreta. `SUSPENDED` puede existir en ambos modelos, pero su alcance y consecuencias son distintos.
* **Casa vacía no es casa suspendida:** Una casa vacía no tiene residentes activos; una casa suspendida tiene restringidos sus privilegios operativos. Ambos atributos pueden coexistir de forma independiente.
* **`Invitation` no es `Access Pass`:** La invitación es la autorización de negocio; el pase es su representación entregada al visitante.
* **`Access Granted` no es `Notification`:** Conceder o rechazar el acceso es el hecho de negocio. Informar al residente es una consecuencia que no altera dicho resultado.
* **`Manual Opening` no es `Emergency Override`:** La apertura manual ordinaria sigue las reglas de acceso; la apertura de emergencia puede eludirlas únicamente para proteger la seguridad física y debe justificarse después.
* **Expiración no es cancelación:** Una invitación se cancela por decisión o por las políticas de desvinculación/suspensión de casa; expira cuando concluye su día de vigencia sin registrar entrada.
