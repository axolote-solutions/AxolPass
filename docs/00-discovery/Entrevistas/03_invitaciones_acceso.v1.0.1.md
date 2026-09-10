# 🧩 Bloque 3: Invitaciones y Control de Acceso

## 🎤 Entrevistas Simuladas (Stakeholders y Analista)

### 🧑‍💼 CommunityAdmin – Administrador del Fraccionamiento

**Analista:** ¿Cómo describirías el proceso actual de acceso para visitantes?

**CommunityAdmin:** El proceso es muy lento. Los guardias tienen que preguntar todo: nombre, placas, destino, y registrarlo en un cuaderno. Eso crea tráfico, molestias, y no garantiza seguridad real.

**Analista:** ¿Qué esperan del nuevo sistema?

**CommunityAdmin:** Queremos que el residente registre a su visitante desde casa, que le llegue un QR al celular del visitante, y que simplemente lo muestre al llegar. Nada de filas.

**Analista:** ¿El código debe tener alguna restricción?

**CommunityAdmin:** Sí, debe funcionar una sola vez y solo en el rango de hora que el residente indicó. Si ya pasó la hora, debe marcar como expirado. Y si alguien intenta usarlo dos veces, debe negarlo.

**Analista:** ¿Todos los residentes pueden generar invitaciones?

**CommunityAdmin:** Sí, pero controlado. Tal vez poner un límite mensual si el reglamento lo exige.

---

### 👨‍👩‍👧‍👦 Residentes (Usuario Principal y Secundario)

**Analista:** ¿Qué tipo de interacción esperan al invitar a alguien?

**Residente Principal:** Que sea rápido. Ingreso el nombre del visitante, su placa, la fecha y hora estimada. Y el sistema le mande un QR al teléfono.

**Analista:** ¿Y después?

**Residente Principal:** El visitante llega, escanea y entra. Yo solo quiero saber si ya entró, o si no pudo pasar. También si el código ya expiró.

**Residente Secundario:** Yo también quiero poder invitar. No quiero depender de mi papá o mamá para registrar a alguien.

**Analista:** ¿Te interesaría recibir notificaciones?

**Residente Secundario:** Solo sobre mis propias invitaciones. El residente principal puede recibir todo.

---

### 👮 Guardia de Seguridad

**Analista:** ¿Qué necesitan ustedes para operar mejor?

**Guardia:** Que el lector sea claro: válido o inválido. Y si hay un error, que diga por qué. Y si la barrera no se abre, que me deje registrar el incidente.

**Analista:** ¿Te gustaría que esos eventos quedaran registrados?

**Guardia:** Sí. Que el administrador vea que fue un error técnico y no culpa mía.

---

### 🛠 SystemAdmin – Axolote Solutions

**Analista:** ¿Qué implica para ustedes el módulo de acceso?

**SystemAdmin:** Debe ser robusto. Cada QR debe ser único, de un solo uso, y con fecha de expiración. Queremos rastrear cada uso y detectar intentos irregulares.

**Analista:** ¿Algo más?

**SystemAdmin:** Sí. El sistema debe permitirnos auditar los accesos y generar estadísticas si hay muchas fallas o intentos inválidos.

---

## 📝 Notas de Campo del Analista

- Todos los actores coinciden en que el proceso actual es lento, manual e inseguro.
- El código QR debe ser **de un solo uso**, con **fecha y hora estimada de acceso**, y debe invalidarse después del ingreso o al expirar.
- La validación en el punto de acceso debe ser inmediata y clara para el guardia.
- Es fundamental mantener registros de entrada, errores y acciones manuales.
- Tanto el residente principal como los secundarios deben poder invitar, con notificaciones diferenciadas.
- La experiencia debe ser simple, rápida y sin fricciones para el visitante.
- La lógica actual solo contempla un ingreso por invitación. Se descarta el concepto de acceso múltiple o visitas recurrentes por ahora.
- Se prevé que las **reglas de uso (ej. número máximo de invitaciones)** puedan variar según el reglamento del fraccionamiento, pero eso será parte de configuraciones por comunidad en el futuro.

---

## 📚 Narrativa Funcional Extendida

En el módulo de **Invitaciones y Control de Acceso**, el objetivo principal es permitir que los **residentes** (usuarios principales y secundarios) generen invitaciones digitales para sus visitantes. Estas invitaciones contienen información clave como el nombre del visitante, placas del vehículo, fecha y hora estimada de la visita.

Una vez registrada la invitación en la aplicación móvil, el sistema **genera un código QR único y de un solo uso**, el cual es enviado directamente al teléfono del visitante. Este código no puede ser reutilizado: si ya se utilizó, se invalida automáticamente. Además, cuenta con una fecha y hora de expiración.

Al llegar al fraccionamiento, el visitante presenta su QR en un **lector físico** ubicado en la caseta de vigilancia. Este lector realiza una validación inmediata:
- Si es válido, se activa la barrera o mecanismo de acceso.
- Si es inválido (por uso previo, expiración, o error), el sistema lo indica al guardia con claridad.

El **guardia** tiene la posibilidad de registrar manualmente incidentes o accesos en caso de fallas del sistema o apertura forzada, lo que garantiza trazabilidad completa.

Por su parte, el **residente** puede consultar el estado de sus invitaciones: si ya fueron utilizadas, si expiraron sin uso, o si hubo intentos fallidos de acceso. Los residentes **reciben notificaciones** en tiempo real ante estos eventos.

El sistema contempla escenarios con múltiples usuarios por casa, y se permite que los usuarios secundarios también puedan generar invitaciones, aunque las notificaciones y visibilidad serán acotadas a sus propias acciones.

Este módulo establece un equilibrio entre **seguridad**, **comodidad** y **velocidad**, transformando un proceso lento y manual en una experiencia digital fluida y segura, tanto para los residentes como para los visitantes y el personal de vigilancia.

