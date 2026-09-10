# 🧩 Bloque 3: Invitaciones y Control de Acceso

## 🎤 Entrevistas Simuladas (Stakeholders y Analista)

### 🧑‍💼 CommunityAdmin – Administrador del Fraccionamiento

**Analista:** ¿Cómo describirías el proceso actual de acceso para visitantes?  
**CommunityAdmin:** El proceso es muy lento. Los guardias tienen que preguntar todo: nombre, placas, destino, y registrarlo en un cuaderno. Eso crea tráfico, molestias, y no garantiza seguridad real.

**Analista:** ¿Qué esperan del nuevo sistema?  
**CommunityAdmin:** Queremos que el residente registre a su visitante desde casa, que le llegue un QR al celular del visitante, y que simplemente lo muestre al llegar. Nada de filas.

**Analista:** ¿El código debe tener alguna restricción?  
**CommunityAdmin:** Sí, debe funcionar una sola vez por entrada y tener una fecha de validez. Aunque ahora entendemos que debe funcionar **todo el día**, desde las 00:00 hasta las 23:59. Pero si ya se usó, debe marcarlo como “ya utilizado”.

**Analista:** ¿Qué pasa si un visitante no sale?  
**CommunityAdmin:** Es importante registrar también la **salida**, para saber si aún está dentro del fraccionamiento. Eso nos permite tener trazabilidad.

**Analista:** ¿Todos los residentes pueden generar invitaciones?  
**CommunityAdmin:** Sí, pero controlado. Tal vez poner un límite mensual si el reglamento lo exige.

---

### 👨‍👩‍👧‍👦 Residentes (Usuario Principal y Secundario)

**Analista:** ¿Qué tipo de interacción esperan al invitar a alguien?  
**Residente Principal:** Que sea rápido. Ingreso el nombre del visitante, su placa, la fecha y hora estimada. Y el sistema le mande un QR al teléfono.

**Analista:** ¿Y después?  
**Residente Principal:** El visitante llega, escanea y entra. Yo quiero saber si ya entró, si tuvo problemas y también si **ya salió**.

**Residente Secundario:** Yo también quiero poder invitar. No quiero depender de mi papá o mamá para registrar a alguien.

**Analista:** ¿Te interesaría recibir notificaciones?  
**Residente Secundario:** Solo sobre mis propias invitaciones. El residente principal puede recibir todo.

---

### 👮 Guardia de Seguridad

**Analista:** ¿Qué necesitan ustedes para operar mejor?  
**Guardia:** Que el lector sea claro: válido o inválido. Y si hay un error, que diga por qué. Y si la barrera no se abre, que me deje registrar el incidente.

**Analista:** ¿Y qué hay del registro de salida?  
**Guardia:** También debería escanear el mismo QR o registrar por placa, para saber que el visitante ya salió. Si no hay QR disponible, debería poder buscar al visitante.

---

### 🛠 SystemAdmin – Axolote Solutions

**Analista:** ¿Qué implica para ustedes el módulo de acceso?  
**SystemAdmin:** Debe ser robusto. Cada QR debe ser único, de un solo uso, y con fecha de expiración. Queremos rastrear cada uso, tanto de entrada como de salida.

**Analista:** ¿Algo más?  
**SystemAdmin:** Sí. El sistema debe permitirnos auditar los accesos y generar estadísticas si hay muchas fallas o entradas sin salida registrada.

---

## 📝 Notas de Campo del Analista

- Todos los actores coinciden en que el proceso actual es lento, manual e inseguro.
- El código QR debe ser **de un solo uso por entrada**, con **fecha válida durante todo el día**.
- Se debe contemplar un **registro de salida**, ya sea escaneando el mismo QR o con otro método (placa, nombre).
- La validación debe ser inmediata y clara para el guardia.
- Tanto el residente principal como los secundarios deben poder invitar, con notificaciones diferenciadas.
- La app del guardia debe permitir registrar accesos, errores, y **salidas manuales si el QR no está disponible**.
- Se contempla configurar reglas por fraccionamiento como: número máximo de invitaciones por casa, o notificaciones extendidas.

---

## 📚 Narrativa Funcional Extendida

El módulo de **Invitaciones y Control de Acceso** de AxolPass permite que los **residentes** (usuarios principales y secundarios) generen invitaciones digitales para sus visitantes. Cada invitación incluye nombre, placas, fecha y hora estimada de visita.

Al generarla, el sistema crea un **código QR único**, válido **solo durante el día completo** de la invitación (de 00:00 a 23:59). El código es **de un solo uso por entrada**. Si ya fue utilizado, se marca como inválido.

Al llegar, el visitante presenta su QR en el lector ubicado en la caseta. El lector valida el QR y autoriza el acceso si es válido. Si hay errores, se informa al guardia con mensajes claros: expirado, ya usado, inválido, etc.

Una vez **dentro del fraccionamiento**, el visitante también debe registrar su **salida** al retirarse. Esto puede hacerse escaneando nuevamente el mismo QR, o bien, el guardia puede registrar la salida manualmente utilizando la placa del vehículo o nombre del visitante.

Todos los accesos (entrada y salida) son **registrados con hora, fecha y responsable**. El **residente** puede consultar si su invitado entró, salió o tuvo problemas. También puede recibir **notificaciones** sobre estos eventos.

Este enfoque permite seguridad, trazabilidad completa, y una experiencia sin fricciones para residentes, visitantes y guardias de seguridad.