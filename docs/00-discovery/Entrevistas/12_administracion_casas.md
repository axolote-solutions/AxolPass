# 🧩 Bloque 11: Configuración por Fraccionamiento

---

## 🎤 Entrevistas Simuladas (Stakeholders y Analista)

### 👨‍💼 CommunityAdmin

**Analista:** ¿Cada fraccionamiento debe tener las mismas reglas?  
**CommunityAdmin:** No. Cada uno tiene sus propias políticas. Algunos permiten más visitantes, otros quieren restringir invitaciones, horarios, incluso qué usuarios pueden invitar.

**Analista:** ¿Qué tipo de configuraciones deberían poder gestionarse?  
**CommunityAdmin:** Límites de invitaciones por mes, horarios de acceso permitidos, cantidad de usuarios por casa, tiempo máximo de validez de QR, si se permite entrada sin QR (modo emergencia), y si el guardia puede registrar acceso manual.

**Analista:** ¿Quién debe tener acceso a cambiar esa configuración?  
**CommunityAdmin:** Solo el usuario administrador de fraccionamiento. Nadie más. Ni los residentes ni los guardias.

##### 👨‍💼 CommunityAdmin

**Analista:** ¿Cada fraccionamiento debe tener las mismas reglas?  
**CommunityAdmin:** No. Cada uno tiene sus propias políticas. Algunos permiten más visitantes, otros quieren restringir invitaciones o definir qué usuarios pueden invitar.

**Analista:** ¿Qué tipo de configuraciones deberían poder gestionarse?  
**CommunityAdmin:** Cosas como: el número máximo de invitaciones por casa, horarios de acceso recomendados para visitas, o la cantidad de usuarios secundarios permitidos por vivienda.

**Analista:** ¿Y sobre los accesos manuales?  
**CommunityAdmin:** Eso depende de cada fraccionamiento. Pero entendemos que **AxoloPass solo facilita el acceso con QR**, y no reemplaza ni limita otras formas de ingreso. Si el guardia deja pasar a alguien sin usar el sistema, eso es responsabilidad interna del fraccionamiento.

**Analista:** ¿La validez del QR puede personalizarse?  
**CommunityAdmin:** En esta versión entendemos que no, y está bien así. Lo importante es que el visitante tenga una ventana razonable de uso.

---

### 🛠 SystemAdmin – Axolote Solutions

**Analista:** ¿Qué configuraciones deben ser definidas por fraccionamiento y cuáles deben ser globales?  
**SystemAdmin:** Cosas como el formato de los correos, el idioma, o el logotipo pueden ser globales o personalizables. Pero reglas como número de invitaciones, duración del QR, permisos de usuarios, todo eso es por fraccionamiento.

**Analista:** ¿Es importante tener auditoría de los cambios?  
**SystemAdmin:** Sí. Todo cambio en configuración debe quedar registrado con fecha, hora y quién lo hizo.

**Analista:** ¿Se permite editar la configuración en cualquier momento?  
**SystemAdmin:** Sí, pero algunos cambios deben tener efecto inmediato (como suspender una casa) y otros podrían requerir reinicio del ciclo de invitaciones.

---

### 👮 Guardia de Seguridad

**Analista:** ¿La configuración les afecta?  
**Guardia:** A veces sí. Si un fraccionamiento cambia las reglas, nosotros necesitamos saberlo. Por ejemplo, si ahora ya no se puede registrar un acceso manual, eso nos afecta directamente.

---

## 📝 Notas de Campo del Analista

- Cada fraccionamiento debe poder personalizar reglas operativas y de seguridad sin afectar a otros.
- El sistema **no impide ni controla accesos alternativos** (manuales, escritos, etc.); estos son externos a AxoloPass.
- La validez del código QR es **fija** en esta versión: todo el día de la invitación (hasta las 00:00 del día siguiente).
- Las configuraciones más importantes incluyen:
  - Número máximo de invitaciones por mes.
  - Horarios válidos para invitaciones.
  - Cantidad de usuarios secundarios permitidos por casa.
- Solo el **CommunityAdmin** puede editar estas configuraciones.
- Todos los cambios deben quedar **auditados** (quién, cuándo, qué cambió).
- Debe existir una **interfaz de configuración clara y segura**.
- Las reglas de cada fraccionamiento se deben reflejar **en tiempo real** en todos los módulos afectados (app, lector, panel del guardia, etc.).

---

## 📚 Narrativa Funcional Extendida

El módulo de **Configuración por Fraccionamiento** permite a cada comunidad definir sus propias reglas operativas y de seguridad dentro del ecosistema **AxolPass**.

Cada fraccionamiento es administrado por un **CommunityAdmin**, quien tiene acceso exclusivo a una **interfaz de configuración avanzada**. A través de esta sección, el administrador puede definir parámetros críticos como:

- **Número máximo de invitaciones mensuales por casa.**
- **Horarios válidos para acceso de visitantes (ej. solo entre 7:00 y 22:00).**
- **Tiempo de validez de los códigos QR generados.**
- **Permisos para acceso manual por parte del guardia (sí/no).**
- **Cantidad de usuarios secundarios permitidos por vivienda.**

Estas configuraciones impactan directamente en el comportamiento del sistema, tanto en la app de los residentes como en los puntos de control de acceso (lectores QR, panel de vigilancia, etc.). Por ejemplo, si se reduce la cantidad de invitaciones permitidas, los usuarios serán notificados en tiempo real.

El sistema garantiza que:
- Todos los cambios en la configuración se **registren con trazabilidad completa** (fecha, hora, usuario, valores anteriores y nuevos).
- Las reglas **solo puedan ser modificadas por el CommunityAdmin.**
- Las nuevas configuraciones se **apliquen inmediatamente o según el tipo de regla.**
- Los guardias sean notificados automáticamente de cambios que impacten su operación (ej. bloqueo de acceso manual, nuevo horario de validación).

Este módulo habilita una arquitectura multi-tenant real, donde cada fraccionamiento opera de forma **autónoma y adaptada a su contexto**, sin perder coherencia ni seguridad a nivel plataforma.
