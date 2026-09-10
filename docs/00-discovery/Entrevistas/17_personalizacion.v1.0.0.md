# 🧩 Bloque 17: Configuración y Personalización por Fraccionamiento

## 🎤 Entrevistas Simuladas (Stakeholders y Analista)

---

### 👩‍💼 CommunityAdmin – Administrador del Fraccionamiento

**Analista:** ¿Qué tipo de configuraciones esperan poder personalizar en AxolPass?

**CommunityAdmin:** Queremos adaptar algunas cosas a nuestro reglamento interno. Por ejemplo, cuántos usuarios puede tener cada casa, si permitimos que los residentes secundarios creen invitaciones o no.

**Analista:** ¿Qué tan seguido cambian esas políticas?

**CommunityAdmin:** No muy seguido, pero es importante que las podamos ajustar si lo aprobamos en asamblea.

**Analista:** ¿Qué otras reglas podría ser necesario ajustar?

**CommunityAdmin:** El número máximo de invitaciones por mes, o si podemos desactivar temporalmente usuarios de una casa que no ha pagado, por ejemplo.

---

### 🛠 SystemAdmin – Axolote Solutions

**Analista:** ¿Qué configuraciones son gestionables por fraccionamiento?

**SystemAdmin:** En esta primera etapa solo permitiremos configuraciones operativas menores. No se puede alterar la lógica del QR o permitir cosas inseguras. Pero sí se puede personalizar:

- Cantidad máxima de usuarios por casa.
- Permitir o no que usuarios secundarios inviten.
- Visibilidad de los accesos.
- Idioma y elementos visuales (nombre del fraccionamiento, colores, logo).
- Período de conservación de historial.

**Analista:** ¿Estas configuraciones las hace el administrador?

**SystemAdmin:** Sí, desde un panel propio, siempre y cuando no contradigan los límites establecidos por el sistema base.

---

## 📝 Notas de Campo del Analista

- Cada fraccionamiento puede tener reglas particulares.
- El sistema debe permitir una **configuración local limitada**, para adaptarse a reglamentos internos.
- Las configuraciones personalizables incluyen:
  - Cantidad de usuarios por casa.
  - Permitir creación de invitaciones por usuarios secundarios.
  - Visibilidad entre residentes (accesos, invitaciones).
  - Límites mensuales de invitaciones (si el reglamento lo exige).
  - Branding (nombre, logo, colores).
- Algunas reglas de seguridad **no son modificables**, como:
  - Validez del QR (único uso por día).
  - Horarios del QR (vigente todo el día).
  - Reutilización de códigos.
- La configuración se realiza desde el **panel de CommunityAdmin**, validando que no se excedan los límites del sistema.

---

## 📚 Narrativa Funcional Extendida

El módulo de **Configuración y Personalización por Fraccionamiento** permite a cada comunidad adaptar ciertos comportamientos del sistema a su **reglamento interno**, sin comprometer la seguridad ni la experiencia general del usuario.

Cada fraccionamiento cuenta con un panel de configuración accesible únicamente para el **CommunityAdmin**, desde donde se pueden ajustar los siguientes parámetros:

- **Cantidad máxima de usuarios por casa.**
- **Permisos para usuarios secundarios** (si pueden invitar, ver historial, etc.).
- **Límites mensuales de invitaciones**, si lo requiere el reglamento local.
- **Visibilidad entre usuarios de la misma casa o entre casas.**
- **Identidad visual personalizada**, como nombre del fraccionamiento, logotipo, colores del portal y mensajes de bienvenida.
- **Período de conservación de historial de actividad.**

AxolPass define un **conjunto de reglas fijas** que **no son modificables** por fraccionamiento, como:

- Un código QR es válido **solo durante el día de la visita**.
- Cada código QR es **de un solo uso**.
- Los códigos QR no se pueden extender ni reutilizar.

Esta separación entre **configuración flexible y seguridad centralizada** garantiza que el sistema pueda adaptarse sin perder integridad operativa ni romper la lógica global de acceso.

Este módulo busca ofrecer una solución adaptable y segura que **respeta la autonomía de cada comunidad**, mientras mantiene un marco común de operación.

---

¿Avanzamos con el 🧩 Bloque 18: Reportes y Exportación de Información?
