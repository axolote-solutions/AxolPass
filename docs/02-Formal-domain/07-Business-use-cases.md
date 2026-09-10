# Casos de Uso de Negocio — MVP AxolPass

## Propósito y alcance

Este documento define los casos de uso conceptuales del negocio para la Fase 1 (MVP) de AxolPass. Es una especificación independiente de tecnología: describe intenciones, condiciones, resultados, reglas y hechos de negocio; no prescribe pantallas, canales de integración, APIs ni mecanismos de implementación.

El alcance se limita a lo definido como MVP en `00-discovery/management/MVPdefinition.md`. Por tanto, se excluyen explícitamente invitaciones recurrentes o por rangos de fechas, preferencias de notificación, pagos automáticos, 2FA, reconocimiento de placas, marca blanca, reportes/exportaciones e integraciones avanzadas.

Los eventos nombrados en este documento son hechos de negocio en pasado. Su registro de auditoría inmutable es transversal: toda operación que afecte la seguridad, la configuración, la vigencia comercial o la identidad de los residentes deja evidencia con fecha y hora, actor y resultado.

---

## 1. Suscripción y habilitación de la comunidad

### UC-NEG-01: Dar de Alta un Fraccionamiento

* **Actor:** `SystemAdmin` de Axolote Solutions.
* **Intención:** Incorporar un fraccionamiento contratado y designar a su administrador local para que pueda prepararlo para operar.
* **Precondiciones del negocio:** Existe un contrato comercial válido; el fraccionamiento no está previamente registrado; la persona designada como `CommunityAdmin` está identificada.
* **Postcondiciones del negocio:** Existe una comunidad con su capacidad contratada y vigencia comercial; un `CommunityAdmin` queda asociado a ella; la comunidad queda activa o pendiente de inicio según la fecha pactada.
* **Reglas de negocio:**
  * Solo Axolote Solutions puede dar de alta comunidades; no existe autoalta pública.
  * La capacidad contratada corresponde al total de casas o lotes y no puede ser parcial.
  * La capacidad, tarifa y ciclo acordados conservan la condición contractual original.
  * Un mismo `CommunityAdmin` puede administrar más de una comunidad.
  * Si la fecha de inicio es futura, se permite preparar la comunidad, pero no operar accesos ordinarios ni generar invitaciones hasta su inicio efectivo.
* **Eventos del negocio:** `FraccionamientoDadoDeAlta`, `AdministradorDeComunidadAsignado`, `ComunidadActivada` o `ComunidadPendienteDeInicio`.

### UC-NEG-02: Registrar Pago Manual de la Suscripción

* **Actor:** `SystemAdmin` de Axolote Solutions.
* **Intención:** Registrar un pago recibido y actualizar la cobertura comercial del fraccionamiento.
* **Precondiciones del negocio:** La comunidad existe y tiene condiciones de cobro vigentes; se conoce el importe, fecha y referencia única del pago.
* **Postcondiciones del negocio:** El pago queda incorporado al historial financiero y la vigencia pagada se extiende; si la nueva vigencia cubre la fecha actual, la comunidad vuelve a operar.
* **Reglas de negocio:**
  * La referencia de pago es única y el mismo pago no puede aplicarse dos veces.
  * En el MVP solo se aceptan pagos equivalentes a ciclos completos de la tarifa pactada; no hay abonos parciales ni saldos a favor.
  * La vigencia se extiende desde la cobertura histórica existente, nunca se reduce ni se reinicia desde la fecha de registro.
  * Un pago no puede extender la cobertura más allá de la vigencia legal del contrato.
  * El registro de un pago no sustituye ni borra el historial de suspensión o deuda previa.
* **Eventos del negocio:** `PagoManualRegistrado`, `VigenciaDeSuscripcionExtendida` y, cuando corresponda, `SuscripcionReactivada`.

### UC-NEG-03: Suspender Comunidad por Falta de Pago

* **Actor:** Sistema, o `SystemAdmin` de Axolote Solutions en una intervención autorizada.
* **Intención:** Aplicar el apagón comercial a una comunidad cuya cobertura pagada venció.
* **Precondiciones del negocio:** La comunidad está activa y su vigencia pagada expiró, o existe una causa comercial autorizada para suspenderla.
* **Postcondiciones del negocio:** La suscripción queda suspendida y el apagón comercial está activo para toda la comunidad.
* **Reglas de negocio:**
  * El apagón comercial impide nuevas invitaciones y operaciones administrativas ordinarias de escritura.
  * Las invitaciones pendientes previas se conservan, pero no habilitan nuevas entradas mientras dure el apagón; expiran de forma normal si vence su día de vigencia.
  * Nunca se bloquean las salidas, las aperturas de emergencia ni los registros necesarios para preservar la seguridad física y la trazabilidad.
  * Suspender una comunidad no elimina sus datos, su historial financiero ni los registros de acceso.
  * La operación es idempotente: una comunidad ya suspendida no debe generar suspensiones repetidas.
* **Eventos del negocio:** `SuscripcionSuspendida`, `ApagonComercialActivado`, `AdministradorDeComunidadAlertado`.

---

## 2. Gestión de la comunidad y sus residentes

### UC-NEG-04: Configurar Reglas Operativas de la Comunidad

* **Actor:** `CommunityAdmin`.
* **Intención:** Establecer las reglas base con las que el fraccionamiento controla sus visitas y su capacidad de estacionamiento.
* **Precondiciones del negocio:** La comunidad fue dada de alta y el actor es su administrador autorizado.
* **Postcondiciones del negocio:** La comunidad dispone de zona horaria, límite diario de invitaciones por casa, capacidad de visitantes y política de aforo aplicables.
* **Reglas de negocio:**
  * La zona horaria define el inicio y cierre del día operativo de las invitaciones.
  * Los límites de invitaciones y espacios de visitantes son cantidades no negativas.
  * El MVP contempla políticas de aforo **estricta**, **de excepción** y **flexible**. La política estricta rechaza el ingreso que consume aforo al alcanzar el límite. Los criterios de autorización y registro de las políticas de excepción y flexible deben formalizarse como políticas de negocio antes de su adopción operativa; no están definidos en el material de descubrimiento.
  * Una reducción de límites no revoca invitaciones ya creadas; impide nuevas invitaciones si la casa ya supera el nuevo límite.
* **Eventos del negocio:** `ReglasDeComunidadConfiguradas`.

### UC-NEG-05: Registrar Casas de la Comunidad

* **Actor:** `CommunityAdmin`.
* **Intención:** Incorporar las unidades privativas que podrán recibir residentes y visitas.
* **Precondiciones del negocio:** La comunidad está habilitada para su preparación; existen reglas operativas básicas; el actor administra la comunidad.
* **Postcondiciones del negocio:** Cada casa registrada queda identificada, pertenece a la comunidad y nace activa, aunque puede permanecer sin residentes.
* **Reglas de negocio:**
  * Se admite el alta individual o por bloques de numeración; ambos producen casas individualmente identificables.
  * La nomenclatura de una casa es única dentro de la comunidad, sin distinguir variaciones irrelevantes de mayúsculas o espacios.
  * El número total de casas no puede superar la capacidad contratada.
  * Una casa vacía es un estado válido y no pierde su identidad ni su historia.
* **Eventos del negocio:** `CasaRegistrada`.

### UC-NEG-06: Asignar Residente Principal

* **Actor:** `CommunityAdmin`.
* **Intención:** Designar al titular residente de una casa, responsable de la gestión cotidiana de sus visitantes y residentes secundarios.
* **Precondiciones del negocio:** La casa existe, pertenece a la comunidad del actor, está activa y no tiene un residente principal activo.
* **Postcondiciones del negocio:** La persona queda vinculada como `ResidentMain` de la casa y puede ejercer los privilegios asociados.
* **Reglas de negocio:**
  * Solo el `CommunityAdmin` puede asignar el residente principal.
  * Una casa tiene como máximo un residente principal activo.
  * La misma persona no puede duplicar su vínculo con la misma casa.
* **Eventos del negocio:** `ResidentePrincipalAsignado`.

### UC-NEG-07: Delegar Residencia Secundaria

* **Actor:** `ResidentMain`; el `CommunityAdmin` puede hacerlo excepcionalmente para su comunidad.
* **Intención:** Vincular a un familiar o conviviente como `ResidentSecondary` de la casa para que pueda generar invitaciones.
* **Precondiciones del negocio:** La casa está activa, tiene residente principal y la persona a vincular está identificada.
* **Postcondiciones del negocio:** La persona queda vinculada como residente secundario activo de la casa.
* **Reglas de negocio:**
  * El residente principal solo puede delegar en su propia casa; el administrador solo en su comunidad.
  * No puede duplicarse una vinculación activa de residencia en una misma casa.
  * La cantidad de residentes activos no puede superar el límite de cuentas por casa acordado por Axolote Solutions.
  * Los residentes secundarios pueden generar invitaciones, pero no administrar la casa ni delegar nuevos residentes.
* **Eventos del negocio:** `ResidenteSecundarioRegistrado`.

### UC-NEG-08: Desvincular Residentes de una Casa

* **Actor:** `CommunityAdmin`, o `ResidentMain` respecto de sus residentes secundarios.
* **Intención:** Terminar una relación de residencia por mudanza, fin de contrato o corrección administrativa.
* **Precondiciones del negocio:** Existe un vínculo de residencia activo y el actor tiene autoridad para terminarlo.
* **Postcondiciones del negocio:** El vínculo queda inactivo; las invitaciones futuras pendientes del residente desvinculado quedan canceladas; se preserva la historia de residencia y accesos.
* **Reglas de negocio:**
  * El residente principal solo puede desvincular a residentes secundarios de su casa.
  * El `CommunityAdmin` puede desvincular a cualquier residente de su comunidad.
  * Al desvincular al residente principal se desvincula también, en cascada, a los residentes secundarios de la casa.
  * Una visita ya iniciada conserva la posibilidad de registrar su salida.
* **Eventos del negocio:** `ResidenteDesvinculado`, `InvitacionCanceladaPorDesvinculacion`.

### UC-NEG-09: Suspender o Reactivar una Casa

* **Actor:** `CommunityAdmin`.
* **Intención:** Restringir o restituir temporalmente la operación de una casa por morosidad interna, sanción o resolución administrativa.
* **Precondiciones del negocio:** La casa pertenece a la comunidad administrada; para suspenderla existe un motivo explícito.
* **Postcondiciones del negocio:** La casa queda suspendida o activa conforme a la decisión; al suspender, sus invitaciones pendientes futuras quedan canceladas.
* **Reglas de negocio:**
  * Una casa suspendida no puede generar invitaciones ni recibir nuevos accesos ordinarios.
  * La suspensión de una casa es distinta del apagón comercial: afecta solo a esa casa y no al resto del fraccionamiento.
  * No se cancelan ni bloquean salidas de visitas que ya ingresaron.
  * La suspensión y la reactivación deben quedar justificadas y auditadas.
* **Eventos del negocio:** `CasaSuspendida` o `CasaReactivada`, `InvitacionCanceladaPorSuspensionDeCasa`.

---

## 3. Invitaciones y control de acceso

### UC-NEG-10: Generar Invitación de Un Solo Uso

* **Actor:** `ResidentMain` o `ResidentSecondary`.
* **Intención:** Autorizar una visita para una fecha determinada y entregar al visitante un pase de entrada de un solo uso.
* **Precondiciones del negocio:** La comunidad no tiene apagón comercial; la casa está activa; el actor es residente activo de la casa; la identidad de contacto del actor está validada.
* **Postcondiciones del negocio:** Existe una invitación pendiente para el día indicado, asociada a la casa y visitante; el visitante recibe un pase con representación QR y PIN numérico.
* **Reglas de negocio:**
  * La invitación requiere los datos mínimos del visitante, una hora estimada y su clasificación de visita.
  * La hora estimada sirve para referencia operativa; el pase es válido durante todo el día programado en la zona horaria de la comunidad.
  * Una invitación es de un solo uso para la entrada: después de registrar el ingreso no admite otra entrada hasta que se complete su salida.
  * La invitación no puede crearse si se excede el límite diario de la casa.
  * Solo las visitas vehiculares con permanencia consumen aforo; peatones, transporte transitorio y entregas transitorias no lo consumen.
* **Eventos del negocio:** `InvitacionGenerada`, `PaseDeAccesoEntregado`.

### UC-NEG-11: Cancelar Invitación Pendiente

* **Actor:** El residente que creó la invitación; `ResidentMain` respecto de invitaciones de su casa; o `CommunityAdmin` cuando exista una causa administrativa.
* **Intención:** Revocar una visita que aún no ha ingresado.
* **Precondiciones del negocio:** La invitación existe, pertenece al ámbito del actor y permanece pendiente.
* **Postcondiciones del negocio:** La invitación queda cancelada y su pase deja de habilitar un ingreso.
* **Reglas de negocio:**
  * Una invitación ya usada para entrar no puede cancelarse; debe permanecer disponible para registrar la salida.
  * Una invitación expirada, completada o ya cancelada no cambia nuevamente de estado.
  * La cancelación conserva la evidencia de la invitación y de quién la canceló.
* **Eventos del negocio:** `InvitacionCancelada`.

### UC-NEG-12: Validar y Registrar Entrada Programada

* **Actor:** Visitante, asistido por `SecurityGuard` cuando sea necesario.
* **Intención:** Determinar si un pase presentado habilita la entrada y registrar el resultado de la visita.
* **Precondiciones del negocio:** Se presenta un pase asociado a una invitación; la comunidad se encuentra operativa para nuevos ingresos.
* **Postcondiciones del negocio:** Se registra una entrada concedida o rechazada; cuando se concede, la invitación queda en uso y se actualiza el aforo si aplica.
* **Reglas de negocio:**
  * Solo puede entrar una invitación pendiente, vigente para el día actual, perteneciente a una casa activa y no suspendida.
  * No son utilizables invitaciones canceladas, expiradas, completadas o ya en uso.
  * Bajo política estricta, una visita que consume aforo se rechaza si no hay espacio disponible.
  * Bajo las políticas de excepción y flexible, el resultado se determina conforme a sus políticas de negocio formalizadas; el MVP aún no fija sus criterios de autorización ni de registro.
  * El rechazo también es un hecho auditable e identifica su motivo de negocio.
* **Eventos del negocio:** `AccesoConcedido` o `AccesoRechazado`, y cuando corresponda `EspacioDeVisitanteOcupado`.

### UC-NEG-13: Registrar Salida de Visitante

* **Actor:** Visitante, o `SecurityGuard` cuando deba intervenir.
* **Intención:** Cerrar la visita de una persona que ya ingresó y, cuando aplique, liberar el espacio de visitante ocupado.
* **Precondiciones del negocio:** La invitación o registro de visita indica una entrada previa en curso.
* **Postcondiciones del negocio:** La visita queda completada y el aforo correspondiente se libera.
* **Reglas de negocio:**
  * Una salida debe poder registrarse incluso si la comunidad está en apagón comercial o la casa fue suspendida después de la entrada.
  * Una visita que cruzó la medianoche puede salir usando el pase con el que registró su entrada; el pase no habilita una nueva entrada.
  * Solo se libera un espacio cuando la visita originalmente lo consumió.
* **Eventos del negocio:** `SalidaRegistrada` y, cuando corresponda, `EspacioDeVisitanteLiberado`.

### UC-NEG-14: Registrar Llegada Sorpresa

* **Actor:** `SecurityGuard`.
* **Intención:** Gestionar la llegada de una persona sin invitación previa mediante autorización tradicional y registro formal.
* **Precondiciones del negocio:** Existe una persona sin pase válido; el guardia identifica la casa destino y cuenta con autorización para operar en la comunidad.
* **Postcondiciones del negocio:** La llegada queda admitida o rechazada, con el guardia responsable y la información disponible de la visita; si ingresa, queda habilitado su posterior registro de salida.
* **Reglas de negocio:**
  * La ausencia de invitación no elimina las reglas ordinarias: la casa debe poder recibir visitas y el apagón comercial impide nuevos ingresos ordinarios.
  * La visita se clasifica para aplicar la política de aforo correspondiente.
  * El guardia debe registrar la autorización y una observación cuando la situación lo requiera.
  * Una llegada sorpresa no es, por sí misma, una emergencia.
* **Eventos del negocio:** `AccesoManualRegistrado`, `AccesoManualConcedido` o `AccesoManualRechazado`.

### UC-NEG-15: Registrar Apertura Manual o Contingencia de Caseta

* **Actor:** `SecurityGuard`.
* **Intención:** Dejar trazabilidad de una intervención manual por fallo de lectura, fallo de barrera u otra contingencia operativa.
* **Precondiciones del negocio:** Existe una anomalía de operación o se requiere una apertura manual; el guardia es identificable.
* **Postcondiciones del negocio:** La intervención y su resultado quedan registrados; si implicó acceso o salida, queda asociada a la visita, casa o contingencia correspondiente.
* **Reglas de negocio:**
  * Toda apertura manual requiere motivo y responsable.
  * La contingencia no autoriza automáticamente a ignorar las reglas ordinarias de acceso; si se autoriza una entrada sin pase se aplica UC-NEG-14.
  * La apertura de emergencia para proteger la vida, integridad, propiedad o vialidad puede eludir las restricciones ordinarias, pero exige justificación posterior y conserva evidencia inmutable.
  * Ninguna contingencia puede impedir la salida segura de personas o vehículos.
* **Eventos del negocio:** `AperturaManualRegistrada`, `ContingenciaRegistrada`, y cuando aplique `AperturaDeEmergenciaEjecutada`.

### UC-NEG-16: Expirar Invitaciones No Utilizadas

* **Actor:** Sistema.
* **Intención:** Cerrar las invitaciones cuyo día válido terminó sin haber registrado una entrada.
* **Precondiciones del negocio:** Existe una invitación pendiente cuya fecha de vigencia concluyó conforme a la zona horaria de la comunidad.
* **Postcondiciones del negocio:** La invitación queda expirada y su pase deja definitivamente de permitir una entrada.
* **Reglas de negocio:**
  * Solo una invitación pendiente puede expirar por falta de uso.
  * Una invitación en uso no expira por este caso de uso, pues debe conservarse para registrar la salida.
  * La expiración es irreversible y no consume ni libera aforo, porque no existió ingreso.
* **Eventos del negocio:** `InvitacionExpirada`.

---

## 4. Notificación y trazabilidad

### UC-NEG-17: Notificar Resultado de un Acceso

* **Actor:** Sistema, originado por un hecho de acceso o rechazo.
* **Intención:** Informar al residente pertinente sobre el ingreso o rechazo de su visitante.
* **Precondiciones del negocio:** Ocurrió un acceso concedido o rechazado asociado a una invitación o a una llegada sorpresa; existe un destinatario residente activo según la relación con la casa y la invitación.
* **Postcondiciones del negocio:** Se registra una notificación de acceso dirigida al residente; el hecho de acceso conserva su validez aunque la notificación no se entregue.
* **Reglas de negocio:**
  * El `ResidentMain` puede ser informado de las visitas de su casa.
  * El `ResidentSecondary` recibe avisos de sus propias invitaciones, no de toda la casa.
  * Las alertas de seguridad o administrativas críticas no pueden suprimirse mediante preferencias ordinarias; las preferencias de notificación rutinaria están fuera del MVP.
  * La notificación no modifica el resultado del acceso ni puede retrasar una decisión de seguridad física.
* **Eventos del negocio:** `ResidenteNotificadoDeAcceso` o `ResidenteNotificadoDeRechazo`.

### UC-NEG-18: Consultar Historial de Accesos

* **Actor:** `ResidentMain`, `ResidentSecondary` o `CommunityAdmin`.
* **Intención:** Revisar la evidencia de accesos e incidentes dentro del ámbito que corresponde al actor.
* **Precondiciones del negocio:** El actor tiene una relación activa y autorizada con la casa o comunidad consultada; existen registros históricos o se admite un resultado vacío.
* **Postcondiciones del negocio:** El actor obtiene una consulta de los eventos permitidos sin alterar su evidencia original.
* **Reglas de negocio:**
  * Los residentes ven la actividad de su casa; el residente secundario se limita a sus propias invitaciones y el principal puede revisar las de la casa.
  * El `CommunityAdmin` ve los eventos de su comunidad con la privacidad ordinaria preservada: no se muestran datos personales completos de visitantes en consultas rutinarias.
  * Todo registro conserva al menos fecha y hora, tipo de evento, actor o responsable identificable cuando corresponda, casa asociada y resultado.
  * Los registros son inmutables y se conservan conforme a la política de retención definida para el servicio.
* **Eventos del negocio:** No aplica; es una consulta que no cambia el estado del negocio.
