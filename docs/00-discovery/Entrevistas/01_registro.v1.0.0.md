# 🗂️ Bloque 1 — Registro, Autenticación y Contratos

## 🎤 Entrevista Simulada (Stakeholder: SystemAdmin de Axolote Solutions)

**Analista:** Gracias por tu tiempo. ¿Cómo comienza el ciclo de vida de un fraccionamiento en AxolPass?

**SystemAdmin:** Nosotros, como parte del equipo de Axolote Solutions, somos quienes damos de alta a un nuevo fraccionamiento en el sistema. Esto ocurre una vez que se ha concretado un contrato. A partir de ahí, se registra el número de casas que tendrá, se define la duración del contrato, y se habilita al administrador principal del fraccionamiento, el llamado *CommunityAdmin*.

**Analista:** ¿Se puede registrar más de un fraccionamiento a la vez?

**SystemAdmin:** Sí, y cada uno es independiente. Cada fraccionamiento tiene su propio número de casas, su *CommunityAdmin* y sus propios datos contractuales. Incluso podrían tener fechas de inicio diferentes o distintas condiciones de servicio.

**Analista:** ¿Qué ocurre si un fraccionamiento no paga?

**SystemAdmin:** Si no hay pago registrado para una fecha límite, el sistema automáticamente suspende el acceso a todos los usuarios del fraccionamiento. También lo notificamos previamente. Esta suspensión no elimina datos; solo detiene el servicio.

**Analista:** ¿El *SystemAdmin* puede desactivar manualmente un fraccionamiento?

**SystemAdmin:** Sí, pero debe registrar una justificación. Puede ser por una solicitud especial o una anomalía detectada. Todo debe quedar registrado.

---

## 🎤 Entrevista Simulada (Stakeholder: CommunityAdmin de un fraccionamiento)

**Analista:** Una vez que tu fraccionamiento es dado de alta por Axolote, ¿cuál es el primer paso?

**CommunityAdmin:** Lo primero que hago es ingresar al sistema y comenzar el registro de las casas. Pero antes, tengo que autenticarme como *CommunityAdmin*, claro. Es un usuario privilegiado.

**Analista:** ¿Cómo es el proceso de autenticación?

**CommunityAdmin:** Accedemos con usuario y contraseña. Para nosotros, como administradores, se recomienda el uso de autenticación en dos pasos (2FA). Nos da seguridad. En el futuro podríamos evaluar opciones biométricas.

**Analista:** ¿Y para los residentes?

**CommunityAdmin:** Para los *ResidentMain* y *ResidentSecondary* es más sencillo. Un login con correo y contraseña, aunque sería bueno dejar abierta la posibilidad de otras formas de autenticación para versiones futuras.

---

## 🎤 Entrevista Simulada (Stakeholder: ResidentMain)

**Analista:** ¿Cómo accediste a la app por primera vez?

**ResidentMain:** Fui registrado por el administrador del fraccionamiento. Él me envió una invitación con un código de activación o enlace. Desde ahí me registré como *residente principal*, puse mis datos y creé mi contraseña.

**Analista:** ¿Tuviste algún problema con el proceso?

**ResidentMain:** No, aunque creo que sería útil que la app permitiera guardar credenciales o tener sesión persistente, al menos para los residentes. También sería bueno poder recuperar la cuenta fácilmente en caso de olvidar la contraseña.

---

## 📝 Notas de Campo del Analista

- El proceso de **registro de fraccionamientos** lo realiza Axolote Solutions desde su panel de control.
- Cada fraccionamiento tiene datos clave:
  - Nombre
  - Número de casas
  - Fecha de inicio y fin del contrato
  - Estado (activo, suspendido, vencido)
- El *SystemAdmin* puede **desactivar un fraccionamiento manualmente**, pero debe registrar una causa.
- El sistema debe **suspender automáticamente el servicio** por falta de pago o contrato vencido.
- Los fraccionamientos deben poder **reactivarse si el pago se realiza** o si se renueva el contrato.
- **Los residentes y administradores tienen roles bien diferenciados**, con distintos niveles de autenticación:
  - *SystemAdmin* y *CommunityAdmin* → con 2FA
  - *ResidentMain* y *ResidentSecondary* → con contraseña simple inicialmente
- El proceso de incorporación de usuarios es **cerrado y controlado por el fraccionamiento**.
- Es importante garantizar un **sistema de recuperación de cuenta confiable**.
- El **registro de casas** comienza después del alta del fraccionamiento y forma parte del flujo de onboarding.
- Se sugiere dejar abierta la opción a futuro para:
  - Autenticación biométrica
  - Autenticación federada (Google, Apple ID, etc.)

---

## 📚 Narrativa Funcional Extendida

> El ciclo de vida de un fraccionamiento comienza en manos de **Axolote Solutions**, donde el equipo comercial formaliza un contrato y el **SystemAdmin** lo da de alta en el sistema. Este registro contiene datos clave como el número de casas, el rango de fechas del contrato y la designación del primer administrador (*CommunityAdmin*).
>
> Una vez habilitado, el **CommunityAdmin** accede mediante credenciales privilegiadas, protegidas idealmente por un segundo factor de autenticación (2FA). A través de su panel, comienza el registro de las casas y la asignación de residentes principales.
>
> Los **ResidentMain** acceden mediante un proceso de invitación, donde completan su registro y definen su contraseña. Tienen la posibilidad de invitar a usuarios secundarios (familiares, personal de servicio, etc.), quienes también tendrán acceso a la aplicación.
>
> El sistema debe poder suspender automáticamente un fraccionamiento completo si no hay pagos registrados al vencimiento del contrato. Esta suspensión bloquea todo el acceso al sistema, aunque se mantiene la integridad de los datos.
>
> Además, el *SystemAdmin* puede desactivar o reactivar un fraccionamiento de manera manual, registrando siempre una justificación.
>
> Las políticas de autenticación son diferenciadas por tipo de usuario, y se espera que evolucionen en versiones futuras para mejorar la experiencia y la seguridad, con opciones como autenticación biométrica, autenticación federada o recuperación simplificada de credenciales.
