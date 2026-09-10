# 🧩 Bloque 3: Invitaciones y Control de Acceso

## 🎤 Entrevistas Simuladas (Stakeholders y Analista)

### 🧑‍💼 CommunityAdmin – Administrador del Fraccionamiento

> “El proceso actual de entrada a nuestro fraccionamiento es lento y genera muchas molestias. Los guardias tienen que pedir nombre, placas, destino y registrar manualmente todo en un cuaderno. A veces hay tráfico, otras veces el visitante se molesta. Nosotros necesitamos una forma más ágil y segura. La idea de enviar un código QR al visitante suena perfecta.”

> “Nos gustaría tener la certeza de que el código QR funciona solo una vez. Si ya entró, no puede volver a entrar con el mismo. También debería tener fecha y hora de expiración. Muchos residentes reciben visitas recurrentes, por eso sería útil que el sistema permita configurar la fecha y hora aproximada de llegada, y que se registren los accesos con precisión.”

> “Sabemos que cada casa puede tener varios habitantes, y todos deberían poder invitar, pero el control debe venir del administrador de la casa. Nosotros desde la administración solo queremos garantizar que no haya abuso. Tal vez poner un límite de invitaciones por casa sería útil si el reglamento lo indica.”

**Analista:** ¿Qué esperan del nuevo sistema?  
**CommunityAdmin:** Queremos que el residente registre a su visitante desde casa, que le llegue un QR al celular del visitante, y que simplemente lo muestre al llegar. Nada de filas.

**Analista:** ¿Qué pasa si un visitante llega fuera de la hora indicada?  
**CommunityAdmin:** Anteriormente pensábamos que eso debía invalidar el acceso, pero ya no. El código debe seguir funcionando mientras sea el mismo día. Si es para el viernes, debe servir desde cualquier hora del viernes hasta las 00:00 del sábado.

**Analista:** ¿Qué sucede si el mismo código se intenta usar dos veces?  
**CommunityAdmin:** Si ya se usó una vez ese día, debe marcar como "ya utilizado". Pero si no se ha usado y es el día correcto, debe permitir el ingreso.

**Analista:** ¿El guardia puede ver el nombre y la placa del visitante?  
**CommunityAdmin:** Sí, pero no puede modificar nada. Solo visualizar y validar.

---

### 👨‍👩‍👧‍👦 Residentes (Usuario Principal y Secundario)

**Analista:** ¿Qué tipo de interacción esperan al invitar a alguien?  
**Residente Principal:** Que sea rápido. Ingreso el nombre del visitante, su placa, la fecha y hora estimada. Y el sistema le mande un QR al teléfono.

> “Una vez creada la invitación, que el sistema le mande un mensaje al visitante con un código QR. Que él solo llegue, escanee en la entrada y entre. Sin tanto rollo.”

> “Me gustaría ver si ya entró o no, y si alguien intentó entrar y fue rechazado porque ya había usado el código. También sería bueno recibir notificaciones si hubo algún problema.”

**Residente Secundario:** Yo también quiero poder invitar. No quiero depender de mi papá o mamá para registrar a alguien.

**Analista:** ¿Te interesaría recibir notificaciones?  
**Residente Secundario:** Solo sobre mis propias invitaciones. El residente principal puede recibir todo.

---

### 👮 Guardia de Seguridad

**Analista:** ¿Qué necesitan ustedes para operar mejor?  
**Guardia:** Que el lector sea claro: válido o inválido. Y si hay un error, que diga por qué. Y si la barrera no se abre, que me deje registrar el incidente.

> “Lo que necesitamos es rapidez y claridad. El visitante llega, escanea el código en el lector, y en segundos me debe decir: válido o inválido. Si es válido, se abre la barrera. Si no, que diga por qué: expirado, ya usado, QR falso, etc.”

> “También me gustaría que quede registrado si tuve que abrir manualmente por algún fallo del lector, o si hubo un error de validación. Que esas cosas queden guardadas para que luego el administrador revise.”

---

### 🛠 SystemAdmin – Axolote Solutions

**Analista:** ¿Qué implica para ustedes el módulo de acceso?  
**SystemAdmin:** Debe ser robusto. Cada QR debe ser único, de un solo uso, y con fecha de expiración. Queremos rastrear cada uso y detectar intentos irregulares.

> “Desde Axolote, nos interesa garantizar que los accesos están controlados. El uso de códigos QR de un solo uso con fecha de expiración es clave. Debemos registrar la hora de entrada real, no solo la programada.”

> “Además, debemos ser capaces de auditar el uso de cada código y detectar comportamientos anómalos, como múltiples intentos con el mismo código, accesos fallidos repetidos, etc.”

---

## 📝 Notas de Campo del Analista

- La validez de la invitación aplica para **todo el día indicado**, no por hora.
- Los códigos QR deben funcionar desde **00:00 hasta las 23:59** del día de la visita.
- **Un código QR solo puede usarse una vez.**
- Residentes secundarios pueden generar sus propias invitaciones.
- La app del guardia permite visualizar detalles, registrar accesos, e ingresar datos manualmente en casos excepcionales.
- Se contempla registrar errores de lectura o apertura manual por parte del guardia.
- Los stakeholders coinciden en que el proceso actual es lento y propenso a errores.
- Debe haber notificaciones para los residentes sobre el uso o intento de uso del código.
- Se deben registrar accesos exitosos y fallidos para trazabilidad.
- Se espera permitir configuración futura para reglas como límite de invitaciones o acceso recurrente.

---

## 📚 Narrativa Funcional Extendida

En el módulo de **Invitaciones y Control de Acceso**, el objetivo principal es permitir que los **residentes** (usuarios principales y secundarios) generen invitaciones digitales para sus visitantes. Estas invitaciones contienen información clave como el nombre del visitante, placas del vehículo, fecha y hora estimada de la visita.

Una vez registrada la invitación en la aplicación móvil, el sistema **genera un código QR único y de un solo uso**, el cual es enviado directamente al teléfono del visitante. Este código no puede ser reutilizado: si ya se utilizó, se invalida automáticamente. Además, cuenta con una fecha y hora de expiración.

Por diseño, **Axolote Solutions y el fraccionamiento no deben asumir el contexto exacto de cada visita**, por lo que una invitación permanece válida durante **todo el día de su emisión**, desde las 00:00 hasta las 23:59. Si un visitante llega antes o después de la hora estimada, el sistema debe seguir permitiendo el acceso mientras la fecha coincida.

Al llegar al fraccionamiento, el visitante presenta su QR en un **lector físico** ubicado en la caseta de vigilancia. Este lector realiza una validación inmediata:
- Si es válido, se activa la barrera o mecanismo de acceso.
- Si es inválido (por uso previo, expiración, o error), el sistema lo indica al guardia con claridad.

El **guardia** tiene la posibilidad de registrar manualmente incidentes o accesos en caso de fallas del sistema o apertura forzada, lo que garantiza trazabilidad completa.

Por su parte, el **residente** puede consultar el estado de sus invitaciones: si ya fueron utilizadas, si expiraron sin uso, o si hubo intentos fallidos de acceso. Los residentes **reciben notificaciones** en tiempo real ante estos eventos.

El sistema contempla escenarios con múltiples usuarios por casa, y se permite que los usuarios secundarios también puedan generar invitaciones, aunque las notificaciones y visibilidad serán acotadas a sus propias acciones.

Este módulo establece un equilibrio entre **seguridad**, **comodidad** y **velocidad**, transformando un proceso lento y manual en una experiencia digital fluida y segura, tanto para los residentes como para los visitantes y el personal de vigilancia.