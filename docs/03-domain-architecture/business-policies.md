# Políticas de Negocio — Arquitectura de Dominio

## Propósito y alcance

Este catálogo organiza las políticas de negocio explícitamente definidas para el MVP según los bounded contexts involucrados. Una política describe la consecuencia obligatoria de un hecho de negocio cuando se cumple su condición; no describe una iniciativa de actor ni define APIs, eventos técnicos, procesos, tiempos de ejecución o mecanismos de transporte.

El contenido se deriva de [Políticas de Negocio — MVP AxolPass](../02-Formal-domain/08-Business-policies.md), contrastado con los [Casos de Uso de Negocio](../02-Formal-domain/07-Business-use-cases.md) y la [Arquitectura de Bounded Contexts](./bounded-context-architecture.md). No amplía el comportamiento documentado.

## Convenciones de lectura

- Los nombres de los disparadores y eventos se conservan como hechos de negocio.
- **Contextos involucrados** identifica los límites que participan según sus responsabilidades formales; no prescribe una integración técnica.
- Cuando una política produce comunicación o evidencia, esa consecuencia no altera el hecho original que la originó.

## 1. Suscripción y operación comercial

| ID | Política | Contextos involucrados | Disparador y condición | Acción obligatoria | Restricciones y resultado |
| --- | --- | --- | --- | --- | --- |
| POL-NEG-01 | Habilitación en la Fecha de Inicio | Suscripción y Facturación; Gestión de Comunidad | `FechaDeInicioDeComunidadAlcanzada`; la comunidad está pendiente de inicio, el contrato sigue vigente y existe cobertura para la fecha. | La comunidad pasa a activa y queda habilitada para operación ordinaria. | No adelanta la fecha acordada ni activa una comunidad cancelada o suspendida por falta de cobertura. Produce `ComunidadActivada`. |
| POL-NEG-02 | Aplicación del Apagón Comercial | Suscripción y Facturación; Control de Accesos; Notificaciones y Alertas | `SuscripcionSuspendida`; la suspensión corresponde a una comunidad comercialmente vigente hasta ese momento. | Se activa el apagón comercial y se informa al `CommunityAdmin` la suspensión y su motivo. | Impide invitaciones, ingresos ordinarios y escritura administrativa ordinaria; no elimina datos, no cancela registros y no bloquea salidas, emergencias ni trazabilidad. Produce `ApagonComercialActivado` y `AdministradorDeComunidadAlertado`. |
| POL-NEG-03 | Restitución del Servicio por Cobertura Recuperada | Suscripción y Facturación; Gestión de Comunidad; Control de Accesos | `VigenciaDeSuscripcionExtendida`; la comunidad está suspendida y la vigencia cubre la fecha actual. | Se reactiva la suscripción y se desactiva el apagón comercial. | Solo restituye operación comunitaria; no reactiva casas suspendidas ni invitaciones canceladas o expiradas por causa propia. Produce `SuscripcionReactivada` y `ApagonComercialDesactivado`. |

## 2. Casas y residentes

| ID | Política | Contextos involucrados | Disparador y condición | Acción obligatoria | Restricciones y resultado |
| --- | --- | --- | --- | --- | --- |
| POL-NEG-04 | Protección de Capacidad Contratada | Gestión de Comunidad; Suscripción y Facturación | `CasaRegistrada`; la incorporación alcanza la capacidad total contratada. | La comunidad queda sin disponibilidad para registrar casas adicionales hasta una modificación comercial autorizada. | Las casas existentes continúan operando; una necesidad administrativa local no autoriza exceder la capacidad. Produce `CapacidadDeComunidadAlcanzada`. |
| POL-NEG-05 | Baja en Cascada de Residentes Secundarios | Gestión de Comunidad; IAM | `ResidenteDesvinculado`; la persona era `ResidentMain` de la casa. | Se desvincula a cada `ResidentSecondary` activo de esa casa y esta queda sin residentes activos. | Se conserva la historia; no se elimina la casa ni se cambian relaciones de otras casas. Produce `ResidenteDesvinculado` para cada secundario afectado. |
| POL-NEG-06 | Revocación de Invitaciones por Desvinculación | Gestión de Comunidad; Control de Accesos | `ResidenteDesvinculado`; el residente tiene invitaciones futuras pendientes. | Se cancelan las invitaciones y sus pases dejan de permitir entrada. | Las visitas ya ingresadas conservan su salida; no se borra evidencia. Produce `InvitacionCanceladaPorDesvinculacion`. |
| POL-NEG-07 | Revocación de Invitaciones por Suspensión de Casa | Gestión de Comunidad; Control de Accesos | `CasaSuspendida`; la casa tiene invitaciones futuras pendientes. | Se cancelan las invitaciones pendientes de la casa. | No afecta otras casas, ni cancela visitas en curso, ni impide sus salidas. Produce `InvitacionCanceladaPorSuspensionDeCasa`. |

## 3. Invitaciones, accesos y aforo

| ID | Política | Contextos involucrados | Disparador y condición | Acción obligatoria | Restricciones y resultado |
| --- | --- | --- | --- | --- | --- |
| POL-NEG-08 | Entrega del Pase de Acceso | Control de Accesos; Notificaciones y Alertas | `InvitacionGenerada`; la invitación sigue pendiente y contiene contacto del visitante. | Se entrega al visitante un pase de un solo uso con QR y PIN numérico por correo electrónico y WhatsApp. | La entrega no cambia vigencia ni estado; el pase sigue sujeto a reglas de uso, fecha, casa y aforo. Produce `PaseDeAccesoEntregado`. |
| POL-NEG-09 | Ocupación de Aforo por Entrada | Control de Accesos; Gestión de Comunidad | `AccesoConcedido` o `AccesoManualConcedido`; la visita es vehicular con permanencia. | Se registra la ocupación de un espacio de visitante. | Peatones, entregas y transporte transitorio no ocupan aforo. Bajo política estricta no se supera el límite. Produce `EspacioDeVisitanteOcupado`. |
| POL-NEG-10 | Liberación de Aforo por Salida | Control de Accesos; Gestión de Comunidad | `SalidaRegistrada`; la visita había ocupado espacio al ingresar. | Se libera un espacio de visitante. | Solo se libera una vez y únicamente si se consumió; no se borra la entrada o salida. Produce `EspacioDeVisitanteLiberado`. |
| POL-NEG-11 | Cierre de Invitación al Concluir la Visita | Control de Accesos | `SalidaRegistrada`; la salida corresponde a una invitación en uso. | La invitación queda completada y no habilita otra entrada. | Aplica incluso con apagón comercial o casa suspendida después de la entrada; una salida no se reutiliza como entrada. Produce `InvitacionCompletada`. |
| POL-NEG-12 | Expiración de Invitación sin Uso | Control de Accesos; Gestión de Comunidad | `DiaDeVigenciaDeInvitacionConcluido`; la invitación sigue pendiente y no tuvo entrada en su día, conforme a la zona horaria de la comunidad. | La invitación expira y su pase deja de ser válido para entrada. | Una invitación en uso no expira por esta política; la expiración es irreversible y no cambia el aforo. Produce `InvitacionExpirada`. |
| POL-NEG-13 | Protección de Salida Segura | Control de Accesos; Suscripción y Facturación; Gestión de Comunidad | `ApagonComercialActivado`, `CasaSuspendida` o `AperturaDeEmergenciaEjecutada`; existe una visita ingresada con salida pendiente. | Se preserva la posibilidad de registrar y realizar la salida. | Ninguna restricción comercial, administrativa o de aforo puede impedir una salida segura. No concede una nueva entrada ni anula la restricción registrada. No produce evento adicional. |

## 4. Comunicación, trazabilidad y emergencias

| ID | Política | Contextos involucrados | Disparador y condición | Acción obligatoria | Restricciones y resultado |
| --- | --- | --- | --- | --- | --- |
| POL-NEG-14 | Alerta de Resultado de Acceso | Control de Accesos; Gestión de Comunidad; Notificaciones y Alertas | `AccesoConcedido`, `AccesoRechazado`, `AccesoManualConcedido` o `AccesoManualRechazado`; hay una casa con residente destinatario activo. | Se informa el resultado al residente correspondiente. | El `ResidentMain` puede recibir eventos de su casa; el `ResidentSecondary`, los de sus invitaciones. No demora ni modifica la decisión; la no entrega no invalida el acceso ni revierte un rechazo. Produce `ResidenteNotificadoDeAcceso` o `ResidenteNotificadoDeRechazo`. |
| POL-NEG-15 | Trazabilidad Inmutable de Hechos Relevantes | Transversal a los contextos que producen los hechos listados | Cualquier hecho que afecte seguridad, invitación, acceso, aforo, configuración, residencia o vigencia comercial; el hecho se produjo, fue rechazado o requirió intervención manual o emergencia. | Se incorpora evidencia inmutable con fecha/hora, tipo, responsable identificable cuando aplique, comunidad o casa y resultado. | Los actores operativos no modifican ni eliminan evidencia. La consulta se limita al ámbito autorizado y preserva la privacidad ordinaria del visitante. Produce `HechoDeNegocioAuditado`. |
| POL-NEG-16 | Registro Reforzado de Intervenciones Manuales y Emergencias | Control de Accesos; IAM; trazabilidad transversal | `AperturaManualRegistrada` o `AperturaDeEmergenciaEjecutada`; intervino un `SecurityGuard` identificable. | Se registran motivo, responsable, resultado y, cuando se conoce, la visita o casa; una emergencia queda pendiente de justificación posterior. | Una apertura manual ordinaria no elude reglas. La emergencia puede hacerlo para proteger vida, integridad, propiedad o vialidad, pero debe justificarse y conservar evidencia. Produce `ContingenciaRegistrada` y, al regularizarse, `ContingenciaJustificada` o `IncidenteDeSeguridadNoJustificado`. |

## Políticas pendientes de definición o delimitación

Esta sección no agrega políticas. Registra únicamente vacíos y discrepancias explícitas que deben resolverse antes de extender el comportamiento del dominio.

| Área pendiente | Evidencia | Información que falta para formalizarla |
| --- | --- | --- |
| **Aforo de excepción y flexible** | Los casos de uso del MVP contemplan ambas políticas, pero solo definen el rechazo al alcanzar el límite para aforo estricto. | Criterios de autorización, registro, consumo de aforo y tratamiento de excepciones para cada política. |
| **Políticas automáticas de IAM** | IAM es un bounded context formalizado, pero el catálogo formal de políticas no define una política disparada automáticamente para el ciclo de vida de identidad, rol o alcance. | Disparador, condición, acción obligatoria, restricciones y hechos resultantes, si el negocio requiere tales automatismos. |
| **Notificación de cancelación de invitación** | El Event Storming formal enumera una política para notificar al visitante al cancelar una invitación; el catálogo formal de políticas y los casos de uso del MVP no la definen. | Confirmación de si aplica al MVP y, de aplicar, destinatario, disparador, condición, acción, restricciones y resultado. |
| **Retención de evidencia auditable** | La trazabilidad debe conservarse conforme a una política de retención del servicio, pero la duración y reglas de dicha retención no están definidas. | Plazo, ámbito, reglas de conservación, acceso y disposición de la evidencia. |

## Fuera de alcance

No se definen políticas para reportes o exportaciones, preferencias ordinarias de notificación, pagos automáticos, integraciones avanzadas, dispositivos, lectores, canales técnicos adicionales o detalles de ejecución. Esos elementos están excluidos del MVP o no tienen una política de negocio formal en la documentación fuente.
