# Políticas de Negocio — MVP AxolPass

## Propósito y alcance

Las políticas de este documento expresan decisiones que el negocio aplica automáticamente como consecuencia de un hecho ocurrido. Complementan los casos de uso de negocio en [07-Business-use-cases.md](./07-Business-use-cases.md): no describen la iniciativa de un actor ni su implementación, sino el efecto obligatorio que debe ocurrir cuando se cumple una condición.

Se incluyen únicamente políticas dentro del alcance del MVP. Los disparadores son hechos de negocio; no se prescribe cómo ni cuándo se transportan, procesan o detectan.

---

## 1. Políticas de suscripción y operación comercial

### POL-NEG-01: Habilitación en la Fecha de Inicio

* **Disparador:** `FechaDeInicioDeComunidadAlcanzada`.
* **Condición:** La comunidad está pendiente de inicio, su contrato sigue vigente y cuenta con cobertura comercial para la fecha de inicio.
* **Acción del negocio:** La comunidad pasa a estar activa y queda habilitada para su operación ordinaria.
* **Restricciones:** Esta política no adelanta una fecha de inicio pactada ni activa una comunidad cancelada o suspendida por falta de cobertura.
* **Eventos del negocio resultantes:** `ComunidadActivada`.

### POL-NEG-02: Aplicación del Apagón Comercial

* **Disparador:** `SuscripcionSuspendida`.
* **Condición:** La suspensión corresponde a una comunidad comercialmente vigente hasta ese momento.
* **Acción del negocio:** Se activa el apagón comercial para toda la comunidad y se informa al `CommunityAdmin` de la suspensión y su motivo.
* **Restricciones:** El apagón impide nuevas invitaciones, nuevos ingresos ordinarios y operaciones administrativas ordinarias de escritura. No cancela los registros existentes, no elimina datos y nunca bloquea salidas, aperturas de emergencia ni la trazabilidad asociada.
* **Eventos del negocio resultantes:** `ApagonComercialActivado`, `AdministradorDeComunidadAlertado`.

### POL-NEG-03: Restitución del Servicio por Cobertura Recuperada

* **Disparador:** `VigenciaDeSuscripcionExtendida`.
* **Condición:** La comunidad está suspendida y la nueva vigencia pagada cubre la fecha actual.
* **Acción del negocio:** Se reactiva la suscripción y se desactiva el apagón comercial.
* **Restricciones:** La reactivación solo restituye la operación comunitaria. No reactiva casas suspendidas ni invitaciones que hayan sido canceladas o expiradas por una causa propia.
* **Eventos del negocio resultantes:** `SuscripcionReactivada`, `ApagonComercialDesactivado`.

---

## 2. Políticas de casas y residentes

### POL-NEG-04: Protección de Capacidad Contratada

* **Disparador:** `CasaRegistrada`.
* **Condición:** La incorporación de una casa alcanza la capacidad total contratada por la comunidad.
* **Acción del negocio:** La comunidad queda sin disponibilidad para registrar casas adicionales hasta que exista una modificación comercial autorizada del contrato.
* **Restricciones:** La política no impide operar las casas ya registradas ni autoriza exceder la capacidad por una necesidad administrativa local.
* **Eventos del negocio resultantes:** `CapacidadDeComunidadAlcanzada`.

### POL-NEG-05: Baja en Cascada de Residentes Secundarios

* **Disparador:** `ResidenteDesvinculado`.
* **Condición:** La persona desvinculada era el `ResidentMain` de una casa.
* **Acción del negocio:** Se desvincula a cada `ResidentSecondary` activo de la misma casa; la casa queda sin residentes activos.
* **Restricciones:** La historia de las residencias se conserva. La política no elimina la casa ni modifica la relación de residentes de otras casas.
* **Eventos del negocio resultantes:** `ResidenteDesvinculado` para cada residente secundario afectado.

### POL-NEG-06: Revocación de Invitaciones por Desvinculación

* **Disparador:** `ResidenteDesvinculado`.
* **Condición:** El residente desvinculado tiene invitaciones futuras en estado pendiente.
* **Acción del negocio:** Se cancelan esas invitaciones y sus pases dejan de permitir una entrada.
* **Restricciones:** Las visitas que ya ingresaron no se cancelan y conservan el derecho de registrar su salida. La política no borra la evidencia de las invitaciones ni de la desvinculación.
* **Eventos del negocio resultantes:** `InvitacionCanceladaPorDesvinculacion`.

### POL-NEG-07: Revocación de Invitaciones por Suspensión de Casa

* **Disparador:** `CasaSuspendida`.
* **Condición:** La casa suspendida posee invitaciones futuras en estado pendiente.
* **Acción del negocio:** Se cancelan las invitaciones pendientes de la casa.
* **Restricciones:** La suspensión de la casa no afecta a otras casas de la comunidad. Las visitas en curso no se cancelan ni se impide su salida.
* **Eventos del negocio resultantes:** `InvitacionCanceladaPorSuspensionDeCasa`.

---

## 3. Políticas de invitaciones, accesos y aforo

### POL-NEG-08: Entrega del Pase de Acceso

* **Disparador:** `InvitacionGenerada`.
* **Condición:** La invitación permanece pendiente y contiene los datos de contacto del visitante.
* **Acción del negocio:** Se entrega al visitante el pase de acceso de un solo uso, compuesto por sus representaciones QR y PIN numérico, mediante correo electrónico y WhatsApp.
* **Restricciones:** La entrega no cambia la vigencia ni el estado de la invitación. El pase se entrega directamente al visitante y sigue sujeto a las reglas de uso, fecha, casa y aforo.
* **Eventos del negocio resultantes:** `PaseDeAccesoEntregado`.

### POL-NEG-09: Ocupación de Aforo por Entrada

* **Disparador:** `AccesoConcedido` o `AccesoManualConcedido`.
* **Condición:** La visita que ingresó es vehicular con permanencia.
* **Acción del negocio:** Se registra la ocupación de un espacio de visitante de la comunidad.
* **Restricciones:** No se ocupa aforo para peatones, entregas ni transporte transitorio. Con política de aforo estricta, la ocupación no puede superar el límite configurado; las políticas de excepción y flexible permanecen pendientes de definición operativa.
* **Eventos del negocio resultantes:** `EspacioDeVisitanteOcupado`.

### POL-NEG-10: Liberación de Aforo por Salida

* **Disparador:** `SalidaRegistrada`.
* **Condición:** La visita que salió había ocupado un espacio de visitante al ingresar.
* **Acción del negocio:** Se libera un espacio de visitante de la comunidad.
* **Restricciones:** No se libera capacidad para visitas que no la consumieron. La liberación solo ocurre una vez por visita y no borra el registro de entrada ni de salida.
* **Eventos del negocio resultantes:** `EspacioDeVisitanteLiberado`.

### POL-NEG-11: Cierre de Invitación al Concluir la Visita

* **Disparador:** `SalidaRegistrada`.
* **Condición:** La salida corresponde a una invitación que se encontraba en uso.
* **Acción del negocio:** La invitación queda completada y deja de habilitar cualquier entrada posterior.
* **Restricciones:** Esta política debe ejecutarse aunque la comunidad tenga apagón comercial o la casa se haya suspendido después de la entrada. Una salida no puede reutilizarse como nueva entrada.
* **Eventos del negocio resultantes:** `InvitacionCompletada`.

### POL-NEG-12: Expiración de Invitación sin Uso

* **Disparador:** `DiaDeVigenciaDeInvitacionConcluido`.
* **Condición:** La invitación continúa pendiente y no registró entrada durante su día de vigencia, calculado con la zona horaria de la comunidad.
* **Acción del negocio:** La invitación queda expirada y su pase deja de ser válido para una entrada.
* **Restricciones:** Una invitación en uso no expira mediante esta política, porque debe conservarse para permitir la salida. La expiración es irreversible y no altera el aforo.
* **Eventos del negocio resultantes:** `InvitacionExpirada`.

### POL-NEG-13: Protección de Salida Segura

* **Disparador:** `ApagonComercialActivado`, `CasaSuspendida` o `AperturaDeEmergenciaEjecutada`.
* **Condición:** Existe una visita con entrada previamente registrada y salida pendiente.
* **Acción del negocio:** Se preserva la posibilidad de registrar y realizar la salida de la visita.
* **Restricciones:** Ninguna restricción comercial, administrativa o de aforo puede impedir una salida segura. Esta política no concede una nueva entrada ni anula el registro de la restricción que la originó.
* **Eventos del negocio resultantes:** No aplica; preserva un derecho operativo de la visita en curso.

---

## 4. Políticas de notificación y trazabilidad

### POL-NEG-14: Alerta de Resultado de Acceso

* **Disparador:** `AccesoConcedido`, `AccesoRechazado`, `AccesoManualConcedido` o `AccesoManualRechazado`.
* **Condición:** El acceso se encuentra asociado a una casa con un residente destinatario activo.
* **Acción del negocio:** Se informa el resultado al residente que corresponda: el `ResidentMain` puede recibir los eventos de su casa y el `ResidentSecondary` los de sus propias invitaciones.
* **Restricciones:** La notificación no cambia el resultado del acceso ni puede demorar una decisión de seguridad. La no entrega de la alerta no invalida el acceso ni revierte un rechazo.
* **Eventos del negocio resultantes:** `ResidenteNotificadoDeAcceso` o `ResidenteNotificadoDeRechazo`.

### POL-NEG-15: Trazabilidad Inmutable de Hechos Relevantes

* **Disparador:** Cualquier evento de negocio que afecte la seguridad, una invitación, un acceso, la ocupación de aforo, la configuración de la comunidad, la relación de residencia o la vigencia comercial.
* **Condición:** El hecho se produjo, fue rechazado o requirió una intervención manual o de emergencia.
* **Acción del negocio:** Se incorpora evidencia inmutable del hecho, con fecha y hora, tipo, actor o responsable identificable cuando corresponda, comunidad o casa relacionada y resultado.
* **Restricciones:** La evidencia no puede modificarse ni eliminarse por los actores operativos. La consulta se limita al ámbito de visibilidad autorizado y la consulta administrativa rutinaria preserva la privacidad de la identidad completa del visitante.
* **Eventos del negocio resultantes:** `HechoDeNegocioAuditado`.

### POL-NEG-16: Registro Reforzado de Intervenciones Manuales y Emergencias

* **Disparador:** `AperturaManualRegistrada` o `AperturaDeEmergenciaEjecutada`.
* **Condición:** La intervención fue realizada por un `SecurityGuard` identificable.
* **Acción del negocio:** Se registra el motivo, responsable, resultado y, cuando se conozca, la visita o casa relacionada; una apertura de emergencia queda pendiente de justificación posterior.
* **Restricciones:** Una apertura manual ordinaria no permite eludir por sí misma las reglas de invitación, suspensión o aforo. La apertura de emergencia es excepcional y debe justificarse, pero nunca puede impedir la protección inmediata de personas, propiedad o vialidad.
* **Eventos del negocio resultantes:** `ContingenciaRegistrada` y, cuando se regularice, `ContingenciaJustificada` o `IncidenteDeSeguridadNoJustificado`.

