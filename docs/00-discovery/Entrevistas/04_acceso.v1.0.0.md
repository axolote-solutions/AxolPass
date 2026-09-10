# 🧩 Bloque 4: Registro de Accesos

## 🎤 Entrevistas Simuladas (Stakeholders y Analista)

---

### 🧑‍💼 CommunityAdmin – Administrador del Fraccionamiento

**Analista:** ¿Cuál es su expectativa respecto al registro de entradas y salidas?  
**CommunityAdmin:** Necesitamos saber con precisión quién entra, cuándo entra y si salió. Aunque no siempre registramos la salida, sí sería ideal tener esa información también.

**Analista:** ¿Qué datos son importantes en cada registro?  
**CommunityAdmin:** Fecha, hora, nombre del visitante, casa destino, tipo de acceso (manual o automático), y si fue exitoso o rechazado. También si fue residente o invitado.

**Analista:** ¿Pueden consultar estos registros?  
**CommunityAdmin:** Sí, pero solo para las casas de su fraccionamiento. Necesitamos filtros por fecha, casa y tipo de evento.

---

### 👨‍👩‍👧‍👦 Residentes

**Analista:** ¿Les interesa consultar el historial de accesos?  
**Residente Principal:** Por supuesto. Quiero ver cuándo entraron los visitantes que invité. Sería bueno recibir notificaciones, por ejemplo: "Juan Pérez entró a las 14:20".

**Residente Secundario:** Yo también, pero solo los que yo invité.

**Analista:** ¿Y sobre salidas?  
**Residente Principal:** No es tan importante como la entrada, pero si se puede registrar, mucho mejor.

---

### 👮 Guardia de Seguridad

**Analista:** ¿Ustedes registran manualmente los accesos?  
**Guardia:** Solo cuando falla el lector o si la persona no trae el QR. En ese caso buscamos por nombre o placa y registramos nosotros.

**Analista:** ¿Les gustaría registrar también salidas?  
**Guardia:** No es que nos gustaría, es obligatorio. Cada coche que sale debe ser revisado y registrado, igual que los que entran. La seguridad también implica evitar que salgan personas no autorizadas o que se saquen objetos indebidos.

**Analista:** ¿Qué información agregan ustedes?  
**Guardia:** A veces una nota: “lector dañado”, “acceso manual por falla”, “vehículo no registrado”, etc.

---

### 🛠 SystemAdmin – Axolote Solutions

**Analista:** ¿Cómo debe implementarse el registro de accesos?  
**SystemAdmin:** Cada evento de entrada debe crear un registro con todos los metadatos posibles: QR, visitante, residente, tipo de validación, método (lector, app, manual), éxito o falla, fecha y hora. Para salida igual, si se puede automatizar.

**Analista:** ¿Y la trazabilidad?  
**SystemAdmin:** Es crucial. Cada acción debe quedar registrada para auditoría, especialmente si hay reclamos o intentos de acceso inválidos.

---

## 📝 Notas de Campo del Analista

- El sistema debe generar un **registro de acceso** cada vez que un visitante o residente entra o sale del fraccionamiento.
- Los datos mínimos por registro son: fecha, hora, nombre del visitante o residente, casa destino, tipo de acceso (entrada o salida), resultado (válido, inválido, manual), y método (QR, manual, lector).
- El **registro de salida** no es obligatorio, pero debe estar disponible como funcionalidad opcional o automatizada.
- Los **guardias** pueden registrar accesos manuales con comentarios.
- El **CommunityAdmin** puede consultar el historial filtrando por casa y fecha.
- Los **residentes** tienen visibilidad solo sobre sus propios visitantes.
- Es importante permitir la trazabilidad completa en caso de fallos o reclamos.

---

## 📚 Narrativa Funcional Extendida

El módulo de **Registro de Accesos** en el sistema **AxolPass** permite capturar y consultar todas las acciones relacionadas con el ingreso y salida de personas al fraccionamiento. Esto incluye tanto a visitantes como a residentes.

Cada vez que un código QR es utilizado exitosamente para ingresar, el sistema crea un **registro automático**, con los siguientes campos:

- Nombre del visitante o residente  
- Casa destino  
- Fecha y hora de entrada  
- Método de acceso (lector QR, manual, app)  
- Resultado del intento (válido, ya usado, expirado, etc.)  
- Observaciones (si el guardia intervino manualmente)  

El **registro de salida** puede realizarse de manera manual por parte del guardia, o automatizarse si existe un segundo lector de salida. Es obligatorio, conocer si y cuándo un visitante ha salido.

Estos registros son almacenados de forma permanente, permitiendo trazabilidad y auditoría. El **CommunityAdmin** puede consultar el historial de su fraccionamiento, con filtros por:

- Fecha  
- Número de casa  
- Nombre de visitante  
- Tipo de evento (entrada o salida)  
- Tipo de usuario (residente o visitante)  

Los **residentes** también tienen acceso al historial de sus propias invitaciones, incluyendo si fueron usadas, si expiraron, o si fueron rechazadas.

Por último, el **guardia** puede registrar accesos especiales (manuales), asociando notas descriptivas para eventos no comunes, como una apertura manual o un acceso de emergencia.

El sistema garantiza así una cobertura completa del ciclo de acceso, brindando **seguridad, transparencia y trazabilidad**, elementos clave para la confianza de todos los actores involucrados.
