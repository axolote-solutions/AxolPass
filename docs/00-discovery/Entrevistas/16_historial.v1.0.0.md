# 🧩 Bloque 16: Control de Actividad e Historial

## 🎤 Entrevistas Simuladas (Stakeholders y Analista)

---

### 👩‍💼 CommunityAdmin – Administrador del Fraccionamiento

**Analista:** ¿Qué tipo de seguimiento necesitan sobre los residentes y accesos?  
**CommunityAdmin:** Queremos ver quién generó qué invitaciones, a qué hora se usaron, si se rechazaron, y cualquier incidente. Esto nos ayuda a responder quejas o verificar situaciones.

**Analista:** ¿Revisan esos registros regularmente?  
**CommunityAdmin:** Sí, sobre todo si hay conflictos o inquietudes de seguridad. A veces necesitamos saber si alguien tuvo muchas visitas o si hubo intentos fallidos de entrada.

**Analista:** ¿Qué otros datos les interesa tener?  
**CommunityAdmin:** Último acceso, número de visitas, actividad por casa. Todo esto sirve para tener visibilidad.

---

### 👨‍👩‍👧‍👦 Residentes

**Analista:** ¿Les gustaría revisar su propio historial?  
**Residente Principal:** Claro. Saber si mi invitado entró, a qué hora, o si hubo algún intento de acceso no autorizado.

**Residente Secundario:** Yo solo quiero ver mis invitaciones, no las de toda la casa.

---

### 👮 Guardia de Seguridad

**Analista:** ¿Se registra todo lo que sucede en la caseta?  
**Guardia:** Lo ideal es que sí. Accesos, errores del lector, aperturas manuales, todo. Así si pasa algo, hay evidencia.

---

### 🛠 SystemAdmin – Axolote Solutions

**Analista:** ¿Cómo debe funcionar el historial a nivel sistema?  
**SystemAdmin:** Toda actividad debe ser trazable y auditable. Desde creación de usuarios, cambios de rol, hasta cada intento de acceso. Eso nos permite analizar incidentes o problemas.

**Analista:** ¿Quién debería tener acceso a esta información?  
**SystemAdmin:** Por niveles. Los residentes ven solo lo suyo. El CommunityAdmin ve todo su fraccionamiento. Nosotros solo accedemos si hay un incidente reportado.

---

## 📝 Notas de Campo del Analista

- El sistema debe registrar:
  - Generación de invitaciones.
  - Uso exitoso de códigos QR.
  - Intentos fallidos de acceso.
  - Accesos manuales por parte del guardia.
  - Cambios de rol o eliminación de usuarios.
- El historial debe estar disponible:
  - Para los residentes: solo su actividad.
  - Para el CommunityAdmin: todo el fraccionamiento.
  - Para Axolote Solutions: solo en caso de incidente o auditoría.
- El historial debe incluir:
  - Fecha y hora.
  - Tipo de evento.
  - Usuario responsable.
  - Resultado (éxito, error, motivo).
- La información debe conservarse por un periodo configurable (ej. 12 meses).

---

## 📚 Narrativa Funcional Extendida

El **Control de Actividad e Historial** es una funcionalidad transversal que permite a todos los actores del sistema tener **visibilidad, trazabilidad y respaldo** sobre los eventos ocurridos dentro del ecosistema AxolPass.

Cada acción relevante genera un registro en el sistema, entre ellas:

- **Creación y cancelación de invitaciones.**
- **Intentos de acceso con QR (válidos o inválidos).**
- **Accesos forzados o manuales registrados por el guardia.**
- **Modificaciones en usuarios o roles.**
- **Eliminación de cuentas.**

Estos eventos se almacenan con metadatos clave: fecha, hora, usuario, casa asociada, tipo de evento y resultado.

- Los **residentes** pueden revisar su propia actividad: invitaciones creadas, accesos exitosos o fallidos, y notificaciones recibidas.
- Los **CommunityAdmins** pueden revisar el historial completo de su fraccionamiento, lo cual es útil para auditoría interna o resolución de conflictos.
- Los **SystemAdmins** de Axolote Solutions acceden al historial solo en caso de auditoría técnica o revisión de incidentes reportados por el CommunityAdmin.

La información se presenta en **formatos claros y exportables**, y se conserva por el tiempo definido en la configuración del sistema (por ejemplo, 12 meses).

Este módulo refuerza la **transparencia, responsabilidad y seguridad**, asegurando que cualquier anomalía o reclamo pueda ser investigado de manera objetiva.

