# Estados del Negocio — MVP AxolPass

## Propósito y alcance

Este documento define los ciclos de vida conceptuales que están expresamente respaldados por los casos de uso de negocio del MVP en [07-Business-use-cases.md](./07-Business-use-cases.md), contrastados con los casos de uso de `00-discovery/Contextos`. Cada modelo separa estados válidos, transiciones permitidas y las reglas que las gobiernan.

No se incluyen estados de implementación, conectividad, entrega de mensajes, lectores o interfaces. Tampoco se incluyen transiciones que, aunque aparezcan en documentación de descubrimiento más amplia, no pertenecen a los casos de uso formales del MVP: la cancelación comercial de la suscripción (`INACTIVE`), la inactivación de una casa y el cierre automático de una visita en uso (`AUTO_COMPLETED`).

---

## 1. Estado comercial de la suscripción

### Estados válidos

| Estado | Significado de negocio |
| --- | --- |
| `PENDING_START` | La comunidad fue dada de alta con una fecha de inicio futura. Puede prepararse, pero aún no realiza operación ordinaria de accesos ni genera invitaciones. |
| `ACTIVE` | La comunidad tiene autorización comercial para operar normalmente. |
| `SUSPENDED` | La comunidad está bajo apagón comercial por falta de cobertura. Se restringen nuevas invitaciones, entradas ordinarias y operaciones administrativas ordinarias de escritura. |

### Transiciones válidas

| Origen | Disparador / caso de uso | Destino | Reglas que gobiernan la transición |
| --- | --- | --- | --- |
| — | UC-NEG-01: Dar de Alta un Fraccionamiento | `ACTIVE` | La fecha de inicio pactada corresponde al día de alta. |
| — | UC-NEG-01: Dar de Alta un Fraccionamiento | `PENDING_START` | La fecha de inicio pactada es futura. |
| `PENDING_START` | Fecha de inicio alcanzada / POL-NEG-01 | `ACTIVE` | La fecha de inicio se alcanzó, el contrato sigue vigente y la comunidad cuenta con cobertura comercial para esa fecha. |
| `ACTIVE` | UC-NEG-03: Suspender Comunidad por Falta de Pago | `SUSPENDED` | La vigencia pagada expiró o existe una causa comercial autorizada. La transición activa el apagón comercial. |
| `SUSPENDED` | UC-NEG-02: Registrar Pago Manual / POL-NEG-03 | `ACTIVE` | El pago extiende la vigencia de modo que cubre la fecha actual. |

### Reglas del modelo

* `PENDING_START` no permite operación ordinaria de accesos ni generación de invitaciones; sí permite la preparación administrativa de la comunidad.
* `SUSPENDED` no elimina la comunidad ni su historia. El apagón comercial preserva siempre las salidas, las aperturas de emergencia y los registros necesarios para la seguridad física.
* La transición `SUSPENDED → ACTIVE` no reactiva casas suspendidas ni invitaciones que ya fueron canceladas o expiraron por una causa propia.
* No existe una transición directa `PENDING_START → SUSPENDED` en los casos de uso formales del MVP.

---

## 2. Estado operativo de la casa

### Estados válidos

| Estado | Significado de negocio |
| --- | --- |
| `ACTIVE` | La casa puede ejercer sus privilegios operativos, sujeto al estado comercial de la comunidad y a las demás reglas aplicables. Toda casa registrada nace en este estado. |
| `SUSPENDED` | La casa se encuentra restringida por una decisión administrativa local. Sus residentes no pueden generar invitaciones ni la casa puede recibir nuevos accesos ordinarios. |

### Transiciones válidas

| Origen | Disparador / caso de uso | Destino | Reglas que gobiernan la transición |
| --- | --- | --- | --- |
| — | UC-NEG-05: Registrar Casas de la Comunidad | `ACTIVE` | La casa se registra dentro de la capacidad contratada y con nomenclatura única en la comunidad. |
| `ACTIVE` | UC-NEG-09: Suspender o Reactivar una Casa | `SUSPENDED` | El `CommunityAdmin` tiene autoridad sobre la casa y registra un motivo de suspensión. |
| `SUSPENDED` | UC-NEG-09: Suspender o Reactivar una Casa | `ACTIVE` | El `CommunityAdmin` decide restituir su operación y deja la evidencia correspondiente. |

### Reglas del modelo

* El estado operativo de la casa es independiente del estado comercial de la suscripción. Una suspensión de casa afecta solo a esa casa; el apagón comercial afecta a toda la comunidad.
* La transición a `SUSPENDED` cancela las invitaciones pendientes futuras de la casa. Las visitas que ya ingresaron permanecen en curso hasta registrar su salida.
* Repetir la solicitud del estado actual no genera una nueva transición.
* `INACTIVE` no es un estado operativo soportado por los casos de uso formales del MVP.
* Una casa sin residentes sigue pudiendo estar `ACTIVE`; estar vacía describe su ocupación residencial, no su estado operativo.

---

## 3. Estado del vínculo de residencia

### Estados válidos

| Estado | Significado de negocio |
| --- | --- |
| `ACTIVO` | La persona mantiene un vínculo vigente con la casa, como `ResidentMain` o `ResidentSecondary`. |
| `INACTIVO` | El vínculo terminó, pero se conserva como historia de residencia. La persona deja de tener los privilegios que se derivaban de ese vínculo. |

### Transiciones válidas

| Origen | Disparador / caso de uso | Destino | Reglas que gobiernan la transición |
| --- | --- | --- | --- |
| — | UC-NEG-06: Asignar Residente Principal | `ACTIVO` | Solo el `CommunityAdmin` puede crear el vínculo principal; la casa debe estar activa y no tener otro residente principal activo. |
| — | UC-NEG-07: Delegar Residencia Secundaria | `ACTIVO` | La casa está activa, tiene residente principal y no se supera el límite de residentes acordado. |
| `ACTIVO` | UC-NEG-08: Desvincular Residentes de una Casa | `INACTIVO` | El actor tiene autoridad para terminar el vínculo y este existe en la casa indicada. |

### Reglas del modelo

* Una casa tiene como máximo un vínculo `ACTIVO` de residente principal.
* Un vínculo secundario solo puede existir si hay un residente principal activo en la casa.
* Al quedar inactivo el vínculo del residente principal, los vínculos secundarios activos de la misma casa también pasan a `INACTIVO`.
* La transición a `INACTIVO` no borra la historia. Además, cancela las invitaciones futuras pendientes originadas por el residente desvinculado; una visita ya iniciada conserva su salida.
* El rol de residencia (`ResidentMain` o `ResidentSecondary`) caracteriza al vínculo activo; no es un estado adicional de su ciclo de vida.

---

## 4. Estado de la invitación

### Estados válidos

| Estado | Significado de negocio |
| --- | --- |
| `PENDING` | La invitación fue generada para su día de vigencia y todavía no registra una entrada. |
| `IN_USE` | La visita ingresó con la invitación y su salida permanece pendiente. |
| `COMPLETED` | La visita registró su salida; la invitación ya no permite otra entrada. |
| `CANCELED` | La invitación fue revocada antes de registrar una entrada; su pase no permite el ingreso. |
| `EXPIRED` | Terminó el día de vigencia sin que la invitación pendiente registrara una entrada; su pase no permite el ingreso. |

### Transiciones válidas

| Origen | Disparador / caso de uso | Destino | Reglas que gobiernan la transición |
| --- | --- | --- | --- |
| — | UC-NEG-10: Generar Invitación de Un Solo Uso | `PENDING` | La comunidad y casa están operativas, el residente tiene vínculo activo y no se excede el límite diario de invitaciones de la casa. |
| `PENDING` | UC-NEG-12: Validar y Registrar Entrada Programada | `IN_USE` | La invitación es vigente para el día actual, la casa está activa, no está suspendida, no existe apagón comercial y se cumplen las reglas de aforo aplicables. |
| `PENDING` | UC-NEG-11: Cancelar Invitación Pendiente | `CANCELED` | El actor está autorizado y la invitación aún no registró una entrada. |
| `PENDING` | POL-NEG-06: Revocación de Invitaciones por Desvinculación | `CANCELED` | El residente que originó la invitación fue desvinculado de la casa. |
| `PENDING` | POL-NEG-07: Revocación de Invitaciones por Suspensión de Casa | `CANCELED` | La casa asociada fue suspendida. |
| `PENDING` | UC-NEG-16: Expirar Invitaciones No Utilizadas | `EXPIRED` | Concluyó el día de vigencia de la invitación, conforme a la zona horaria de la comunidad, sin que se registrara una entrada. |
| `IN_USE` | UC-NEG-13: Registrar Salida de Visitante / POL-NEG-11 | `COMPLETED` | Existe una entrada previa en curso. La salida se admite aunque después de la entrada exista apagón comercial o suspensión de la casa. |

### Reglas del modelo

* `COMPLETED`, `CANCELED` y `EXPIRED` son estados finales en el MVP: no admiten nuevas transiciones.
* Una invitación en `IN_USE` no permite una segunda entrada y no puede cancelarse ni expirar por falta de uso.
* La hora estimada no reduce la vigencia: la invitación permanece utilizable durante el día programado.
* El cruce de medianoche no impide la transición `IN_USE → COMPLETED`; el pase ya usado para entrar solo puede emplearse para registrar la salida, no para una nueva entrada.
* Un acceso rechazado no cambia el estado de la invitación; conserva el estado que tenía antes del intento y genera evidencia del rechazo.
* El modelo no incorpora `AUTO_COMPLETED`: el caso de uso formal del MVP únicamente define la expiración de invitaciones pendientes, no el cierre automático de visitas en curso.

---

## 5. Estado de una contingencia de emergencia

### Estados válidos

| Estado | Significado de negocio |
| --- | --- |
| `PENDING_JUSTIFICATION` | Se realizó una apertura de emergencia; la protección inmediata ya ocurrió y falta documentar su motivo. |
| `JUSTIFIED` | La contingencia cuenta con una justificación válida y permanece como evidencia cerrada e inmutable. |
| `UNJUSTIFIED_SECURITY_INCIDENT` | La contingencia no fue justificada dentro de las condiciones establecidas y queda clasificada como incidente de seguridad no justificado. |

### Transiciones válidas

| Origen | Disparador / caso de uso | Destino | Reglas que gobiernan la transición |
| --- | --- | --- | --- |
| — | UC-NEG-15: Registrar Apertura Manual o Contingencia de Caseta | `PENDING_JUSTIFICATION` | La apertura corresponde a una emergencia para proteger vida, integridad, propiedad o vialidad. La apertura ocurre de inmediato y su justificación se exige posteriormente. |
| `PENDING_JUSTIFICATION` | Justificación de contingencia pendiente | `JUSTIFIED` | El guardia aporta una justificación obligatoria y descriptiva. La evidencia queda cerrada e inmutable. |
| `PENDING_JUSTIFICATION` | Vencimiento de la obligación de justificar | `UNJUSTIFIED_SECURITY_INCIDENT` | No se recibió una justificación dentro de las condiciones establecidas. |

### Reglas del modelo

* Este modelo aplica exclusivamente a aperturas de emergencia. Una apertura manual ordinaria queda auditada, pero los casos de uso formales del MVP no le asignan un ciclo de vida adicional.
* La apertura de emergencia puede eludir las restricciones ordinarias de invitación, aforo, suspensión de casa y apagón comercial para proteger de forma inmediata la seguridad física.
* Una contingencia en `PENDING_JUSTIFICATION` no bloquea la respuesta inmediata a otras emergencias, pero debe ser regularizada conforme a las reglas de auditoría.
* `JUSTIFIED` y `UNJUSTIFIED_SECURITY_INCIDENT` son estados finales y preservan la evidencia histórica.
