# 🧩 Bloque 10: Notificaciones y Alertas

---

## 🎤 Entrevistas Simuladas (Stakeholders y Analista)

### 👨‍💼 CommunityAdmin

**Analista:** ¿Qué tipo de notificaciones consideran importantes desde la administración?  
**CommunityAdmin:** Queremos saber si hubo fallas en el lector, accesos inválidos repetidos, si se suspendió alguna casa o si alguien intenta usar un código falso.

**Analista:** ¿Cada cuándo deberían recibir notificaciones?  
**CommunityAdmin:** Solo si son importantes. No queremos spam. Que sean alertas relevantes, no cada acceso normal.

**Analista:** ¿Qué canal prefieren?  
**CommunityAdmin:** App o correo. No queremos depender solo de WhatsApp.

---

### 🛠 SystemAdmin – Axolote Solutions

**Analista:** ¿Qué uso le dan a las notificaciones a nivel del sistema?  
**SystemAdmin:** Las usamos para monitorear eventos críticos: fallos técnicos, patrones sospechosos, intentos de acceso anómalos. También para monitoreo de infraestructura.

**Analista:** ¿Debería haber distinción entre alertas urgentes y generales?  
**SystemAdmin:** Sí. Un error del sistema no es lo mismo que un acceso fallido por QR vencido. Deben categorizarse y manejarse diferente.

---

### 👨‍👩‍👧‍👦 ResidentMain / ResidentSecondary

**Analista:** ¿Qué notificaciones consideran importantes?  
**ResidentMain:** Me gustaría saber si mi visitante ya entró, si hubo un error, si el QR fue rechazado, o si alguien intentó usarlo después.

**ResidentSecondary:** Que me avise solo sobre las invitaciones que yo generé. No quiero ver las de mis papás.

**Analista:** ¿Prefieren notificación en la app o por otro medio?  
**ResidentMain:** En la app está bien. Pero si se puede configurar, mejor.

---

### 👮 Guardia de Seguridad

**Analista:** ¿Ustedes reciben notificaciones?  
**Guardia:** No, pero estaría bien si nos llega una alerta cuando falla el sistema, o si hay intentos de QR falsos.

---

## 📝 Notas de Campo del Analista

- Las notificaciones deben estar **contextualizadas por rol**:
  - **ResidentMain / ResidentSecondary**: estado de invitaciones, acceso exitoso o rechazado.
  - **CommunityAdmin**: eventos críticos, anomalías, suspensiones.
  - **SystemAdmin**: fallos técnicos, actividad anómala, estado de infraestructura.
- Deben existir niveles de severidad:
  - 🔔 Informativas (ej. acceso exitoso)
  - ⚠️ Advertencias (QR vencido, intento duplicado)
  - 🚨 Críticas (falla en lector, intento fraudulento, caída del servicio)
- Es importante permitir al usuario **configurar qué tipo de notificaciones desea recibir** y por qué canal.
- Canales propuestos:
  - App (notificación push)
  - Correo electrónico
  - Dashboard web para administradores

---

## 📚 Narrativa Funcional Extendida

El módulo de **Notificaciones y Alertas** de AxolPass busca mantener informados a todos los usuarios sobre eventos relevantes, según su rol y nivel de responsabilidad, sin generar ruido innecesario ni saturar los canales de comunicación.

Los **residentes** reciben notificaciones directamente en su app:
- Cuando un visitante **accede exitosamente** con su invitación.
- Si la invitación **fue rechazada** (ya usada, QR inválido, etc.).
- Si **expira sin uso**.

Los **residentes secundarios** solo reciben notificaciones relacionadas con **las invitaciones que ellos mismos generaron**. El residente principal tiene visibilidad total de todas las invitaciones vinculadas a su casa.

Por su parte, el **CommunityAdmin** es notificado únicamente de eventos de valor operativo o de seguridad:
- Fallos en el sistema de acceso (lector, conectividad).
- Accesos fallidos reiterados.
- Intentos con códigos QR alterados.
- Cambios administrativos (suspensión/reactivación de casas, usuarios eliminados).

El **SystemAdmin** de Axolote Solutions cuenta con alertas de infraestructura y sistema:
- Caídas del servicio.
- Anomalías estadísticas (picos de intentos fallidos).
- Registros de auditoría críticos.
- Notificaciones sobre componentes integrados (lectores, base de datos, servidores).

Para garantizar un equilibrio entre visibilidad y claridad, el sistema clasifica las notificaciones en distintos niveles de severidad:

| Nivel        | Ejemplo                                 | Destinatarios            |
|--------------|------------------------------------------|---------------------------|
| 🔔 Informativa | Acceso exitoso de un visitante           | ResidentMain, Secondary   |
| ⚠️ Advertencia | Código QR vencido o ya utilizado         | ResidentMain, Guardias    |
| 🚨 Crítica     | Falla técnica, intento de fraude         | CommunityAdmin, SysAdmin  |

Además, se habilita un **panel de configuración de notificaciones** donde cada usuario puede decidir:
- Qué tipos de eventos desea recibir.
- Por qué canal (App, correo, solo dashboard).
- Con qué frecuencia (en tiempo real, resumen diario, etc.).

Este módulo contribuye a la **experiencia proactiva** del sistema, aumentando la confianza y la capacidad de respuesta ante incidentes o eventos importantes, sin sacrificar simplicidad ni saturar al usuario con datos irrelevantes.
