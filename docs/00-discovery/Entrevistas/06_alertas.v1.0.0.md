# 🧩 Bloque 6: Alertas y Notificaciones

---

## 🎤 Entrevistas Simuladas (Stakeholders y Analista)

### 👨‍👩‍👧‍👦 Residentes

**Analista:** ¿Qué tipo de notificaciones esperan recibir los residentes?  
**Residente Principal:** Me interesa saber si mi invitado ya entró, si hubo un intento fallido, o si el código QR expiró sin usarse.

**Residente Secundario:** Yo solo quiero recibir notificaciones de mis propias invitaciones. No las de toda la casa.

**Analista:** ¿De qué forma prefieren recibir las notificaciones?  
**Residente Principal:** Notificaciones push en la app. También sería bueno un correo si algo importante sucede.

---

### 🧑‍💼 CommunityAdmin

**Analista:** ¿Qué tipo de alertas les sería útil como administradores?  
**CommunityAdmin:** Si un guardia registra un acceso manual, si hay intentos fallidos frecuentes en una casa, o si hay muchas visitas nocturnas. También si un lector falla.

**Analista:** ¿Cada administrador recibe todas las alertas?  
**CommunityAdmin:** Deberíamos poder configurar eso. Quizá solo los críticos para todos, y el resto según necesidad.

---

### 🛠 SystemAdmin – Axolote Solutions

**Analista:** ¿Cuál es el objetivo técnico de este módulo?  
**SystemAdmin:** Garantizar que el sistema **notifica en tiempo real** eventos críticos y operativos. Y que esas alertas estén **asociadas a roles y preferencias**.

**Analista:** ¿Se contempla enviar alertas a otras plataformas?  
**SystemAdmin:** En el futuro, podría integrarse con sistemas externos (ej. correo institucional, dashboard de monitoreo).

---

## 📝 Notas de Campo del Analista

- Las notificaciones deben enviarse **en tiempo real**, dependiendo del evento.
- Tipos de eventos clave:
  - Acceso exitoso de visitante
  - Intento fallido (QR inválido o expirado)
  - QR expirado sin uso
  - Acceso registrado manualmente
  - Error del lector
  - Accesos fuera del horario esperado
- **Residentes** deben recibir solo eventos relacionados con sus propias invitaciones.
- **Admins** deben recibir eventos críticos o configurables.
- Se contemplan canales:
  - Notificaciones **push** en app móvil
  - **Correo electrónico** (solo en eventos importantes)
  - Dashboard futuro
- El sistema debe permitir **configuración por rol y tipo de alerta**.

---

## 📚 Narrativa Funcional Extendida

El módulo de **Alertas y Notificaciones** en AxolPass busca mantener informados en tiempo real a los usuarios clave sobre eventos relevantes en el sistema de control de acceso. Esta funcionalidad mejora la **seguridad, la trazabilidad y la experiencia del usuario**.

Para los **residentes**, el sistema enviará notificaciones push desde la app móvil en eventos como:
- Ingreso exitoso del visitante
- Fallo en la lectura del QR
- Intento de uso de un QR expirado
- Expiración sin uso de una invitación

Los **residentes secundarios** recibirán solo notificaciones sobre las invitaciones que ellos mismos hayan generado, mientras que el **residente principal** podrá ver todas las notificaciones relacionadas con su hogar.

Para el **CommunityAdmin**, el sistema puede generar alertas como:
- Registro manual de acceso (por parte del guardia)
- Errores frecuentes en validación
- Picos inusuales de visitas
- Fallas en el hardware de los lectores

Estas alertas se muestran en su panel de control y pueden ser configuradas según criterios de criticidad o relevancia.

Desde el lado técnico, **Axolote Solutions** gestiona las configuraciones de eventos y podrá, en versiones futuras, integrar con servicios de terceros para análisis de datos, sistemas de monitoreo o notificación por correo electrónico corporativo.

El sistema permite una configuración flexible por tipo de usuario:
- Qué tipo de alerta se recibe
- Por qué medio (push, email, dashboard)
- En qué horario
- Nivel de criticidad

Esta estructura garantiza que cada usuario **recibe la información que necesita, ni más ni menos**, y que puede **reaccionar rápidamente ante eventos relevantes**, fortaleciendo así la **operatividad y la seguridad** del fraccionamiento.
