# 🧩 Bloque 3: Invitaciones y Control de Acceso

---

## 🎤 Entrevistas Simuladas (Stakeholders y Analista)

---

### 🧑‍💼 CommunityAdmin – Administrador del Fraccionamiento

**Analista:** ¿Cómo describirías el proceso actual de acceso para visitantes?  
**CommunityAdmin:** El proceso es muy lento. Los guardias tienen que preguntar todo: nombre, placas, destino, y registrarlo en un cuaderno. Eso crea tráfico, molestias, y no garantiza seguridad real.

**Analista:** ¿Qué esperan del nuevo sistema?  
**CommunityAdmin:** Queremos que el residente registre a su visitante desde casa, que le llegue un QR al celular del visitante, y que simplemente lo muestre al llegar. Nada de filas.

**Analista:** ¿El código debe tener alguna restricción?  
**CommunityAdmin:** Sí, debe funcionar una sola vez y solo durante el día especificado por el residente. Si alguien intenta usarlo más de una vez o en otro día, debe marcarse como inválido.

**Analista:** ¿Todos los residentes pueden generar invitaciones?  
**CommunityAdmin:** Sí, tanto principales como secundarios. Pero podrían aplicarse límites según el reglamento del fraccionamiento.

**Analista:** ¿El guardia puede ver el nombre del visitante o modificar algo?  
**CommunityAdmin:** Solo puede visualizar para validar. No puede modificar.

---

### 👨‍👩‍👧‍👦 Residentes (Principal y Secundarios)

**Analista:** ¿Cómo generarías una invitación desde la app?  
**Residente Principal:** Registro nombre del visitante, placas del vehículo y fecha. Me genera un código QR que le mando por WhatsApp o similar.

**Analista:** ¿Te interesa controlar hora exacta?  
**Residente Principal:** No es necesario. El sistema debería aceptar cualquier hora del día indicado.

**Residente Secundario:** Yo también quiero poder invitar a alguien. Que no dependa solo del titular.

**Analista:** ¿Quieren recibir notificaciones?  
**Residente Principal:** Sí, de todo.  
**Residente Secundario:** Solo de mis invitaciones.

---

### 👮 Guardia de Seguridad

**Analista:** ¿Qué esperan del lector de QR?  
**Guardia:** Que sea rápido. Escaneo y me diga “válido” o “inválido” y por qué: expirado, ya usado, etc.

**Analista:** ¿Y si falla el lector?  
**Guardia:** Poder registrar el acceso manualmente y dejar un comentario, como que abrí la barrera manualmente.

---

### 🛠 SystemAdmin – Axolote Solutions

**Analista:** ¿Qué nivel de control esperan del módulo?  
**SystemAdmin:** Total trazabilidad. Cada QR debe tener fecha de expiración, debe poder usarse solo una vez, y quedar todo registrado.

**Analista:** ¿Qué ocurre si un visitante llega antes o después de la hora estimada?  
**SystemAdmin:** El QR debe seguir activo mientras sea el mismo día. No conocemos el contexto de las visitas, así que no debemos invalidar por hora.

**Analista:** ¿Qué quieren auditar?  
**SystemAdmin:** Reintentos fallidos, accesos manuales, QR inválidos, y generar reportes.

---

## 📝 Notas de Campo del Analista

- El proceso actual de acceso es manual, lento e inseguro.
- La experiencia ideal es: el residente genera invitación → visitante recibe QR → lector valida → entrada automática.
- El QR debe ser único, de un solo uso y válido todo el día asignado (00:00 a 23:59).
- Reutilización o uso fuera de fecha = denegado.
- El guardia debe tener una interfaz clara para validar y registrar errores o aperturas manuales.
- Los usuarios secundarios también pueden generar invitaciones.
- Las notificaciones deben estar diferenciadas por tipo de usuario.
- El historial de accesos debe estar disponible tanto para el residente como para fines de auditoría.

---

## 📚 Narrativa Funcional Extendida

El módulo de **Invitaciones y Control de Acceso** en el sistema **AxolPass** permite a los residentes —tanto principales como secundarios— generar invitaciones digitales para visitantes. Al crear una invitación, el usuario ingresa:

- Nombre del visitante  
- Placas del vehículo  
- Fecha estimada de visita  

Una vez enviada la información, el sistema genera un **código QR único**, válido para un solo uso y únicamente durante el día especificado.

Este QR se envía al visitante (por mensaje o app) y puede ser presentado en la entrada del fraccionamiento. Al llegar, el **lector de la caseta** valida:

- Si el QR es válido y no ha sido usado → permite el acceso automáticamente (abre la barrera).
- Si ya fue usado o ha expirado (otro día) → muestra mensaje claro al guardia.

En caso de **fallas técnicas**, el guardia puede registrar el ingreso de forma manual e indicar el motivo (lector dañado, apertura manual, QR ilegible).

Todos los accesos —correctos o fallidos— se registran con:

- Fecha y hora real de entrada  
- Estado del acceso (exitoso, fallido, manual)  
- Casa asociada a la invitación  

Los **residentes** pueden revisar el estado de sus invitaciones en tiempo real y recibir notificaciones: ingreso exitoso, intento fallido, QR expirado, etc.

El sistema también permite:

- Configurar límites de invitaciones por casa (según reglamento).  
- Gestionar accesos por parte de usuarios secundarios.  
- Auditar cada intento de ingreso para seguridad y análisis.  

Finalmente, se ha decidido que **Axolote Solutions no controla ni interpreta el contexto de las visitas**, por lo que una invitación es válida **todo el día del evento**, desde las 00:00 hasta las 23:59. Solo se invalida si se intenta ingresar en un día distinto o si ya fue utilizada.

Este enfoque promueve un equilibrio entre **seguridad**, **usabilidad** y **eficiencia operativa**, mejorando significativamente la experiencia de residentes, visitantes y personal de seguridad.
