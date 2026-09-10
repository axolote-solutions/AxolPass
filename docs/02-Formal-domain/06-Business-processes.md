# Manual Operativo y de Procesos de Negocio: AxolPass

**Introducción**
El presente documento detalla los 9 procesos operativos fundamentales de la plataforma AxolPass. Abarca el ciclo de vida completo del servicio, desde el aprovisionamiento de nuevos fraccionamientos y la gestión de accesos para visitantes, hasta la aplicación de políticas comerciales, manejo de contingencias y auditorías de seguridad.

---

# Proceso 1: Aprovisionamiento y Puesta en Marcha de un Cliente B2B
**Objetivo:** Trasladar un contrato comercial a la realidad operativa de AxolPass, creando un nuevo fraccionamiento cliente y dejándolo preparado para iniciar operaciones.

**Estado Inicial:** El fraccionamiento no existe en AxolPass.
**Actores Involucrados:** SystemAdmin (personal de Axolote Solutions), CommunityAdmin (Administrador del fraccionamiento).

### Pasos del Negocio y Decisiones

**Captura del Contrato Comercial**
El SystemAdmin registra la identidad del cliente y las condiciones comerciales pactadas, incluyendo capacidad máxima contratada de casas, tarifa base, ciclo de facturación y fecha de inicio.
**Reglas Aplicadas:**
* La creación de nuevos fraccionamientos es exclusiva de Axolote Solutions; no existe autoregistro público.
* La capacidad contratada y la tarifa base deben ser valores positivos.
* La capacidad máxima contratada y la tarifa base quedan fijadas conforme a las condiciones originales del contrato.

**Evaluación de Vigencia**
El sistema evalúa la fecha de inicio acordada.
**Reglas Aplicadas:**
* El servicio no puede comenzar en una fecha anterior a la fecha de aprovisionamiento.
* No se permite establecer retroactivamente tiempo de servicio no prestado.

**Decisión:**
* Si la fecha de inicio corresponde al día actual, el contrato nace ACTIVE.
* Si la fecha de inicio es futura, el contrato nace PENDING_START.

**Restricciones Durante PENDING_START**
Mientras el contrato permanezca en PENDING_START:
* se permite completar el aprovisionamiento administrativo;
* se permite provisionar al CommunityAdmin;
* se permite configurar las reglas operativas del recinto;
* se permite registrar las unidades privativas;
* no se permite iniciar la operación ordinaria de accesos;
* los residentes no pueden generar invitaciones;
* los pases de acceso no pueden utilizarse.
* En la fecha efectiva de inicio, el contrato puede transicionar a ACTIVE y habilitar la operación ordinaria.

**Delegación de Autoridad**
* El SystemAdmin designa a la persona que actuará como CommunityAdmin.

**Aprovisionamiento de Identidad**
* El sistema provisiona la cuenta del CommunityAdmin y lo notifica para que pueda acceder al fraccionamiento asignado.

**Configuración Operativa del Recinto**
El CommunityAdmin establece las reglas globales necesarias para operar la comunidad, incluyendo al menos:
* zona horaria;
* capacidad global de estacionamiento para visitantes;
* límites operativos configurables aplicables al recinto.

**Registro de Unidades Privativas**
El CommunityAdmin registra las casas o unidades privativas del fraccionamiento.
**Reglas Aplicadas:**
* No pueden existir dos casas con la misma nomenclatura dentro del mismo fraccionamiento.
* El número total de unidades registradas no puede exceder la capacidad contratada.

### Estado Final
* El contrato queda en estado ACTIVE o PENDING_START.
* El fraccionamiento queda aprovisionado.
* El CommunityAdmin queda asociado al recinto.
* Las reglas operativas globales quedan configuradas.
* Las unidades privativas registradas nacen operativamente activas.
* Si el contrato permanece PENDING_START, la operación ordinaria continúa deshabilitada hasta la fecha efectiva de inicio.

### Eventos Detonados
* Comunidad Aprovisionada.
* Cuenta de Personal Provisionada.
* Reglas de Comunidad Actualizadas.
* Casa Registrada.
* Suscripción Activada, cuando se alcanza la fecha efectiva de inicio.

---

# Proceso 2: Flujo Nominal de Invitación y Acceso de Visitas
**Objetivo:** Permitir que un residente autorice el ingreso de un visitante y que dicho ingreso ocurra de forma segura, controlada y compatible con las reglas operativas del fraccionamiento.

**Estado Inicial:** Existe una visita planificada, pero el visitante todavía no posee autorización de acceso.
**Actores Involucrados:** PrimaryResident, SecondaryResident, Visitante, SecurityGuard o Caseta.

### Pasos del Negocio y Decisiones

**Solicitud de Invitación**
El residente solicita una invitación indicando la fecha prevista y el tipo de visita.

**Clasificación de la Visita**
Toda visita debe clasificarse antes de generar el pase.
La clasificación determina si el acceso consumirá capacidad de estacionamiento.
* Vehículo con permanencia: consume aforo.
* Peatón: no consume aforo.
* Transporte transitorio: no consume aforo.
* Entrega o paquetería transitoria: no consume aforo.
* La clasificación asignada permanece asociada a la invitación durante todo su ciclo de vida.

**Evaluación de Autorización**
Antes de crear la invitación, se verifican las condiciones que permiten a la casa ejercer el privilegio de invitar.
**Reglas Aplicadas:**
* El contrato del fraccionamiento debe encontrarse operativo.
* No debe existir un CommercialBlackout que prohíba nuevas invitaciones.
* La casa debe encontrarse operativamente ACTIVE.
* La casa no debe encontrarse bajo suspensión operativa.
* No debe haberse superado el límite diario de invitaciones aplicable.

**Distinción conceptual:**
* El CommercialBlackout representa una restricción comercial de todo el fraccionamiento.
* La suspensión de una House representa una restricción operativa específica de una unidad privativa.
* Son causas diferentes y no deben considerarse el mismo estado.

**Generación y Distribución del Pase**
Si todas las validaciones son satisfactorias, el sistema genera el pase de acceso y lo distribuye al visitante mediante los canales habilitados.
**Regla Aplicada:**
* Cuando el pase sea distribuido mediante mensajería, la información necesaria para utilizarlo debe entregarse directamente en el mensaje y no depender de enlaces web externos.

**Llegada y Verificación**
El visitante presenta su pase en la caseta.
**Reglas Aplicadas:**
* La invitación debe encontrarse vigente para la fecha de presentación.
* El pase no debe haber registrado previamente un ingreso incompatible con su estado actual.
* Un pase que ya se encuentre IN_USE no puede registrar una segunda entrada.
* Una invitación EXPIRED, CANCELED o COMPLETED no puede ser utilizada.

**Evaluación de Aforo**
Si la invitación fue clasificada como consumidora de estacionamiento:
* se verifica disponibilidad;
* si no existe capacidad, el ingreso se rechaza.
Si la invitación no consume estacionamiento:
* la restricción de aforo no se aplica.

**Concesión del Acceso**
Si todas las validaciones son satisfactorias:
* se concede el ingreso;
* se abre la barrera correspondiente;
* la invitación transiciona de PENDING a IN_USE;
* si consume estacionamiento, se registra la ocupación de un espacio.

**Notificación de Llegada**
* El hecho de que el visitante haya ingresado origina una notificación al residente.
* La confirmación del acceso y la entrega efectiva de la notificación son hechos distintos.
* El acceso permanece válido incluso si la notificación no puede ser entregada.

**Salida del Visitante**
Cuando el visitante abandona el fraccionamiento:
* se registra la salida;
* la invitación transiciona a COMPLETED;
* si la visita consumía estacionamiento, se libera la capacidad utilizada;
* se origina la correspondiente notificación de salida.

**Cierre Automático por Permanencia Anómala**
Si una invitación permanece en IN_USE durante un periodo superior al máximo permitido sin registrar salida:
* la invitación puede cerrarse lógicamente mediante AUTO_COMPLETED;
* el cierre lógico no supone por sí mismo que el vehículo haya abandonado físicamente el recinto;
* si la visita consumía estacionamiento, la liberación del aforo requiere confirmar la situación física o ejecutar posteriormente un proceso de reconciliación.

### Estado Final
El ciclo normal de una invitación es:
* PENDING -> IN_USE -> COMPLETED
También puede terminar mediante:
* PENDING -> CANCELED
* PENDING -> EXPIRED
* IN_USE -> AUTO_COMPLETED

### Eventos Detonados
* Invitación Generada.
* Acceso Concedido.
* Salida Registrada.
* Invitación Expirada.
* Invitación Auto-Completada.
* Los eventos relacionados con notificaciones pertenecen al proceso de entrega de notificaciones y no representan la realización del hecho de negocio original.

---

# Proceso 3: Expiración de Invitaciones No Utilizadas
**Objetivo:** Cerrar automáticamente invitaciones cuya ventana válida de utilización terminó sin que el visitante haya ingresado.

**Estado Inicial:** Invitación en estado PENDING.
**Actores Involucrados:** Sistema.

### Pasos del Negocio y Decisiones
El sistema identifica invitaciones cuya ventana de validez ya terminó.

**Evaluación de Estado**
* Si la invitación continúa PENDING, puede expirar.
* Si ya se encuentra IN_USE, no debe expirar mediante este proceso.
* Si ya se encuentra CANCELED, COMPLETED o EXPIRED, no requiere ninguna acción.

La invitación transiciona:
* PENDING -> EXPIRED
* El pase asociado deja definitivamente de ser válido.

**Reglas Aplicadas**
* La expiración es irreversible.
* Una invitación expirada no puede reactivarse.
* Una invitación expirada no consume capacidad de estacionamiento porque nunca produjo un ingreso.

### Estado Final
* La invitación queda en estado EXPIRED.

### Eventos Detonados
* Invitación Expirada.

---

# Proceso 4: Suspensión Operativa de una Unidad Privativa
**Objetivo:** Retirar temporalmente los privilegios operativos de una casa específica sin afectar comercialmente al resto del fraccionamiento.

**Estado Inicial:** La House se encuentra ACTIVE.
**Actores Involucrados:** CommunityAdmin.

### Pasos del Negocio y Decisiones
El CommunityAdmin determina que una unidad privativa debe ser suspendida.
La casa transiciona a estado SUSPENDED.
A partir de ese momento:
* sus residentes no pueden generar nuevas invitaciones;
* sus privilegios operativos quedan congelados;
* nuevas operaciones sujetas al estado de la casa son rechazadas.

**Tratamiento de Invitaciones Existentes**
* Las invitaciones futuras todavía en estado PENDING asociadas a la casa suspendida son canceladas.
* Las invitaciones que ya se encuentran IN_USE no son canceladas retroactivamente.
  * el visitante conserva la posibilidad de salir;
  * debe mantenerse la trazabilidad de la visita;
  * las operaciones necesarias para finalizar de forma segura el acceso continúan disponibles.

**Reglas Aplicadas**
* La suspensión de una casa no equivale a CommercialBlackout.
* La suspensión solo afecta a la unidad privativa y a los privilegios derivados de ella.
* Nunca se bloquea la salida de una persona que ya se encuentra dentro del recinto.

### Estado Final
* La casa queda SUSPENDED.
* Las invitaciones futuras pendientes quedan CANCELED.
* Las visitas ya iniciadas pueden completar su salida normalmente.

### Eventos Detonados
* Casa Suspendida.
* Invitación Cancelada por Suspensión de Casa.

---

# Proceso 5: Morosidad y Aplicación del Apagón Comercial
**Objetivo:** Restringir temporalmente la prestación comercial del servicio a un fraccionamiento que ha incumplido sus obligaciones de pago, preservando las operaciones indispensables para la seguridad física.

**Estado Inicial:** El contrato se encuentra ACTIVE, pero su fecha de cobertura pagada ha expirado.
**Actores Involucrados:** Sistema, SystemAdmin, CommunityAdmin.

### Pasos del Negocio y Decisiones

**Evaluación de Vigencia Comercial**
Una rutina periódica compara la fecha actual contra la fecha hasta la cual el contrato se encuentra pagado.
**Regla Aplicada:**
* Si la cobertura ha expirado y no existe un periodo de gracia vigente, la suscripción debe considerarse comercialmente vencida.

**Aplicación del Apagón Comercial**
El contrato transiciona:
* ACTIVE -> SUSPENDED
* y se activa el CommercialBlackout.

**Degradación Operativa**
Mientras el CommercialBlackout permanezca activo:
* los residentes no pueden generar nuevas invitaciones;
* el CommunityAdmin no puede ejecutar operaciones administrativas de escritura restringidas;
* la operación ordinaria de nuevos accesos sujeta al servicio comercial queda suspendida.

**Tratamiento de Invitaciones Existentes**
* Las invitaciones PENDING creadas antes del inicio del CommercialBlackout permanecen registradas pero no pueden utilizarse para generar un nuevo ingreso mientras la suspensión comercial continúe.
* No son canceladas únicamente por causa del CommercialBlackout.
* Si el servicio se reactiva antes de que termine su vigencia, vuelven a ser utilizables.
* Si su ventana temporal termina durante la suspensión, expiran normalmente.

**Operaciones que Nunca se Bloquean**
El CommercialBlackout nunca bloquea:
* la salida de personas o vehículos que ya se encuentran dentro;
* el registro necesario para completar dichas salidas;
* las aperturas de emergencia;
* los mecanismos de auditoría requeridos para dichas operaciones.

**Notificación Administrativa**
La suspensión origina una alerta administrativa obligatoria para el CommunityAdmin.
**Regla Aplicada:**
* La alerta no puede ser silenciada por preferencias ordinarias del destinatario.

**Registro del Pago**
Cuando Axolote Solutions recibe el pago, el SystemAdmin lo registra.
**Reglas Aplicadas:**
* El pago debe ser un múltiplo exacto de la tarifa base.
* No se permiten abonos parciales.

**Recalculo de Vigencia**
* El sistema determina cuántos ciclos cubre el pago y extiende paidThroughDate desde la vigencia histórica correspondiente.
* paidThroughDate nunca retrocede.

**Decisión de Reactivación**
* Si la nueva vigencia cubre la fecha actual: SUSPENDED -> ACTIVE y se elimina el CommercialBlackout.
* Si la nueva vigencia continúa vencida, la suscripción permanece suspendida.

### Estado Final
El contrato puede completar:
* ACTIVE -> SUSPENDED -> ACTIVE
* o permanecer SUSPENDED si no recupera vigencia.

### Eventos Detonados
* Suscripción Suspendida.
* Pago Registrado.
* Suscripción Reactivada.

---

# Proceso 6: Cancelación Comercial del Contrato
**Objetivo:** Finalizar permanentemente la relación comercial de un fraccionamiento con AxolPass preservando su información histórica, financiera y de auditoría.

**Estado Inicial:** Contrato existente en un estado que admite cancelación.
**Actores Involucrados:** SystemAdmin.

### Pasos del Negocio y Decisiones
* El SystemAdmin registra la decisión comercial de cancelar el contrato.
* Se registra el motivo de cancelación.

**Evaluación de Efectividad**
La cancelación puede ser inmediata o tener una fecha efectiva definida conforme a las condiciones contractuales.
Cuando la cancelación entra en vigor:
* el contrato deja de autorizar nuevas operaciones comerciales;
* no pueden generarse nuevas invitaciones;
* se deshabilitan las operaciones administrativas sujetas al servicio activo.
* Las operaciones relacionadas con seguridad, salida de personas y auditoría histórica permanecen disponibles conforme a sus respectivas reglas.
* Los registros financieros y operativos existentes no se eliminan.

**Reglas Aplicadas**
* La cancelación no constituye eliminación física del cliente.
* El historial de pagos debe conservarse.
* Los registros de acceso y auditoría deben conservarse.
* La cancelación efectiva es irreversible mediante una simple reactivación operacional; un eventual restablecimiento comercial requiere una decisión comercial explícita.

### Estado Final
* El contrato queda comercialmente cancelado/inactivo.

### Eventos Detonados
* Suscripción Cancelada.

---

# Proceso 7: Llegada Sorpresa sin Invitación
**Objetivo:** Permitir que la caseta gestione de forma controlada a una persona que se presenta sin una invitación previamente emitida.

**Estado Inicial:** Una persona se encuentra en el acceso sin pase válido.
**Actores Involucrados:** SecurityGuard, visitante, residente.

### Pasos del Negocio y Decisiones
* El guardia identifica la casa destino.

**Evaluación Operativa**
* Se determina si la casa puede recibir una visita.
* Si la casa está SUSPENDED, el acceso ordinario no puede registrarse.
* Si existen restricciones comerciales que impiden nuevos ingresos ordinarios, el flujo no puede continuar.
* Estas restricciones no aplican a una emergencia real.
* Si la casa puede recibir la visita, el guardia registra manualmente el acceso.

* Se clasifica la visita según la misma política de consumo de aforo utilizada por las invitaciones planificadas.
* Si la visita consume estacionamiento:
  * se verifica disponibilidad;
  * se registra la ocupación correspondiente.
* El residente recibe una alerta informando la llegada inesperada.

**Reglas Aplicadas**
* El acceso manual debe quedar asociado al guardia que lo autorizó.
* La ausencia de una invitación no elimina las demás reglas ordinarias de acceso.
* El flujo de llegada sorpresa no constituye una operación de emergencia.

### Estado Final
* El visitante ingresa mediante un acceso manual debidamente identificado y auditado, o el ingreso es rechazado.

### Eventos Detonados
* Acceso Manual Registrado.

---

# Proceso 8: Apertura de Emergencia y Resolución de Contingencia
**Objetivo:** Permitir al personal de seguridad actuar inmediatamente ante una situación que represente riesgo para la vida, integridad física, propiedad o vialidad, incluso cuando las reglas ordinarias impedirían el acceso.

**Estado Inicial:** Existe una situación física que requiere una acción extraordinaria inmediata.
**Actores Involucrados:** SecurityGuard.

### Pasos del Negocio y Decisiones

**Detección de la Emergencia**
* El guardia determina que la situación requiere liberar el acceso sin esperar las validaciones ordinarias.

**Apertura Forzada**
* El guardia activa el mecanismo de emergencia.
**Regla Aplicada: Evasión Operativa Absoluta**
* Para ejecutar la apertura se ignoran restricciones ordinarias de: invitación; aforo; suspensión de casa; CommercialBlackout; autorización ordinaria del visitante.
* La barrera se abre inmediatamente.
* Se genera una contingencia asociada al guardia o dispositivo responsable.
* La contingencia permanece pendiente de justificación.

**Rendición de Cuentas**
* Después de controlar la emergencia, el guardia debe registrar una justificación.
**Reglas Aplicadas:**
* La justificación es obligatoria.
* No puede estar vacía.
* Debe permitir comprender el motivo de la intervención.
* El guardia no puede cerrar normalmente su turno mientras tenga contingencias pendientes de justificar.

**Resolución**
* Una vez registrada una justificación válida, la contingencia se considera resuelta.
* El registro final se vuelve inmutable.
* Si la contingencia no es regularizada dentro de las condiciones establecidas, se clasifica como Incidente de Seguridad no Justificado.

### Estado Final
La contingencia termina como:
* Resuelta y justificada; o
* Incidente de Seguridad no Justificado.
* En ambos casos permanece como evidencia histórica.

### Eventos Detonados
* Apertura de Emergencia Detonada.
* Contingencia Resuelta.
* Incidente de Seguridad no Justificado.

---

# Proceso 9: Revelación Excepcional de Información Protegida
**Objetivo:** Permitir el acceso controlado y excepcional a información protegida cuando exista una necesidad legítima de investigación o auditoría, preservando una trazabilidad estricta.

**Estado Inicial:** Existe información protegida cuya visualización ordinaria se encuentra restringida.
**Actores Involucrados:** CommunityAdmin u otro actor expresamente autorizado.

### Pasos del Negocio y Decisiones
* El actor autorizado solicita revelar información protegida relacionada con una visita o incidente.
* El sistema exige una justificación explícita.

**Evaluación**
* Si la justificación o la autoridad del solicitante no son suficientes, la revelación se rechaza.
* Si ambas son válidas, la operación continúa.
* El sistema revela únicamente la información permitida para la investigación.
* Se registra: quién solicitó la revelación; qué información fue revelada; cuándo ocurrió; cuál fue la justificación declarada.
* El registro resultante se vuelve inmutable.

**Reglas Aplicadas**
* La revelación excepcional no elimina las reglas de privacidad; constituye una excepción controlada y auditable.
* Toda revelación debe tener un actor identificable.
* Toda revelación debe estar justificada.
* El registro de auditoría no puede modificarse ni eliminarse.
* La operación de revelar información y el hecho investigado permanecen conceptualmente separados.

### Estado Final
* La información requerida ha sido revelada de forma controlada o la solicitud ha sido rechazada.
* Toda revelación ejecutada permanece registrada como evidencia histórica.

### Eventos Detonados
* Información Protegida Revelada.

