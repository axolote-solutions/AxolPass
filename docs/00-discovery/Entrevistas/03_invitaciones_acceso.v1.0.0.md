# 🧩 Bloque 3: Invitaciones y Control de Acceso

## 🧾 Entrevista simulada – CommunityAdmin (Administrador de Fraccionamiento)

> “El proceso actual de entrada a nuestro fraccionamiento es lento y genera muchas molestias. Los guardias tienen que pedir nombre, placas, destino y registrar manualmente todo en un cuaderno. A veces hay tráfico, otras veces el visitante se molesta. Nosotros necesitamos una forma más ágil y segura. La idea de enviar un código QR al visitante suena perfecta.”

> “Nos gustaría tener la certeza de que el código QR funciona solo una vez. Si ya entró, no puede volver a entrar con el mismo. También debería tener fecha y hora de expiración. Muchos residentes reciben visitas recurrentes, por eso sería útil que el sistema permita configurar la fecha y hora aproximada de llegada, y que se registren los accesos con precisión.”

> “Sabemos que cada casa puede tener varios habitantes, y todos deberían poder invitar, pero el control debe venir del administrador de la casa. Nosotros desde la administración solo queremos garantizar que no haya abuso. Tal vez poner un límite de invitaciones por casa sería útil si el reglamento lo indica.”

---

## 🧾 Entrevista simulada – Residente Principal

> “En mi casa vivimos mi esposa, mis hijos y yo. Todos usamos el app, y nos gustaría que cada quien pueda invitar a sus propios contactos. Que sea algo sencillo: seleccionar el visitante, registrar su nombre, placas, fecha de la visita, y listo.”

> “Una vez creada la invitación, que el sistema le mande un mensaje al visitante con un código QR. Que él solo llegue, escanee en la entrada y entre. Sin tanto rollo.”

> “Me gustaría ver si ya entró o no, y si alguien intentó entrar y fue rechazado porque ya había usado el código. También sería bueno recibir notificaciones si hubo algún problema.”

---

## 🧾 Entrevista simulada – Guardia / Seguridad

> “Lo que necesitamos es rapidez y claridad. El visitante llega, escanea el código en el lector, y en segundos me debe decir: válido o inválido. Si es válido, se abre la barrera. Si no, que diga por qué: expirado, ya usado, QR falso, etc.”

> “También me gustaría que quede registrado si tuve que abrir manualmente por algún fallo del lector, o si hubo un error de validación. Que esas cosas queden guardadas para que luego el administrador revise.”

---

## 🧾 Entrevista simulada – SystemAdmin (Axolote Solutions)

> “Desde Axolote, nos interesa garantizar que los accesos están controlados. El uso de códigos QR de un solo uso con fecha de expiración es clave. Debemos registrar la hora de entrada real, no solo la programada.”

> “Además, debemos ser capaces de auditar el uso de cada código y detectar comportamientos anómalos, como múltiples intentos con el mismo código, accesos fallidos repetidos, etc.”

---

## 📝 Resumen Narrativo del Dominio – Módulo de Invitaciones y Accesos

En el dominio de **AxolPass**, uno de los procesos más críticos es el de **gestión de invitaciones y control de acceso de visitantes**, cuyo objetivo central es ofrecer una experiencia segura, rápida y confiable para los residentes, visitantes y personal de seguridad.

Los **residentes**, tanto principales como secundarios, deben poder generar invitaciones digitales a través de la app móvil. Al crear una invitación, deben ingresar los datos del visitante: nombre, placas del vehículo, fecha y hora aproximada de visita. Una vez registrada, el sistema genera un **código QR único, no reutilizable**, el cual se envía automáticamente al visitante.

Este código solo puede usarse una vez. Si el visitante ya ingresó, no podrá volver a entrar con el mismo QR. El código tiene una **fecha de expiración configurada** al momento de la invitación. Si se vence, no se puede utilizar.

Al llegar al fraccionamiento, el visitante muestra el código QR en un **lector** instalado en la caseta. El lector valida el código en tiempo real y, si es válido, **autoriza automáticamente el acceso** (por ejemplo, abriendo una barrera). Si el código es inválido, el guardia puede ver un mensaje de error detallado: expirado, ya usado, QR inválido, etc. En caso de contingencia (falla del lector), el guardia puede registrar manualmente el acceso y dejar constancia del incidente.

Todos los **accesos son registrados** con fecha y hora, y asociados a la casa correspondiente. Esto permite tanto al residente como al CommunityAdmin revisar el historial. Además, los residentes reciben notificaciones en tiempo real si su invitado accede, si hubo un intento fallido, o si la invitación expiró sin ser usada.

El sistema contempla futuras configuraciones como: límites de invitaciones por casa, visibilidad por parte del administrador, o múltiples entradas en fechas recurrentes, aunque de inicio la lógica es sencilla: **una invitación → un acceso → una entrada autorizada**.
