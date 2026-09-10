# 🧩 Bloque 15: Gestión de Usuarios y Roles

## 🎤 Entrevistas Simuladas (Stakeholders y Analista)

---

### 👩‍💼 CommunityAdmin – Administrador del Fraccionamiento

**Analista:** ¿Cómo gestionan actualmente a los usuarios del fraccionamiento?  
**CommunityAdmin:** Nosotros damos de alta a los residentes, asignándolos a una casa. Uno de ellos es el usuario principal, que puede agregar a su familia. A veces necesitamos suspender cuentas o eliminar usuarios si se mudan.

**Analista:** ¿Cuál es la diferencia entre usuario principal y secundario?  
**CommunityAdmin:** El usuario principal tiene control total de su casa: puede invitar personas, gestionar pagos, ver notificaciones. Los secundarios solo tienen acceso a funciones básicas.

**Analista:** ¿Puedes ver la actividad de cada usuario?  
**CommunityAdmin:** Sería ideal. Saber quién generó invitaciones, quién entró, o si hay actividad sospechosa. También queremos ver cuándo fue el último acceso.

---

### 👨‍👩‍👧‍👦 Residentes

**Analista:** ¿Cómo se organizan dentro de una casa?  
**Residente Principal:** Yo registré la casa y administro a mi familia. Cada quien tiene su cuenta, pero yo puedo quitar acceso si alguien ya no vive aquí.

**Analista:** ¿Los demás pueden hacer lo mismo?  
**Residente Principal:** No. Solo yo puedo agregar o quitar personas. Los demás solo pueden invitar, ver accesos, cosas así.

**Residente Secundario:** Yo solo quiero poder invitar a mis amigos, no necesito gestionar nada más.

---

### 🛠 SystemAdmin – Axolote Solutions

**Analista:** ¿Cómo estructuran el sistema de roles?  
**SystemAdmin:** Hay al menos tres niveles: SystemAdmin (nosotros), CommunityAdmin (por fraccionamiento) y Residentes (divididos en principales y secundarios).

**Analista:** ¿Cómo manejan la revocación de acceso?  
**SystemAdmin:** Si un CommunityAdmin elimina un usuario, se invalida su cuenta y su acceso a todas las funciones. También bloqueamos el acceso físico si tenía alguna invitación activa.

---

## 📝 Notas de Campo del Analista

- El modelo de usuarios contempla jerarquías:
  - **SystemAdmin**: gestiona todo el sistema.
  - **CommunityAdmin**: administra su fraccionamiento.
  - **ResidentMain**: dueño o responsable de la casa.
  - **ResidentSecondary**: miembros de la familia o personas autorizadas.
- Cada casa puede tener varios usuarios, pero **solo uno es principal**.
- Solo el usuario principal puede agregar o quitar otros usuarios de su casa.
- CommunityAdmin puede suspender o eliminar usuarios del fraccionamiento.
- Debe registrarse actividad por usuario (ej. quién generó qué invitaciones).
- Cuando se elimina un usuario:
  - Se revoca su acceso a la app.
  - Se invalidan invitaciones pendientes.
  - No puede acceder físicamente al fraccionamiento.
- El guardia nunca gestiona usuarios.

---

## 📚 Narrativa Funcional Extendida

La **Gestión de Usuarios y Roles** es un pilar fundamental en el ecosistema AxolPass. El sistema contempla una estructura jerárquica claramente delimitada para asegurar orden, control y trazabilidad.

- A nivel superior, los **SystemAdmins** (Axolote Solutions) supervisan la plataforma, sin intervenir directamente en la operación de los fraccionamientos.
- Cada fraccionamiento tiene uno o más **CommunityAdmins**, quienes se encargan de:
  - Dar de alta casas y usuarios.
  - Asignar roles (principal o secundario).
  - Suspender o eliminar cuentas si es necesario.
- Por cada casa registrada, existe un **ResidentMain**, quien tiene autoridad sobre los usuarios asociados a su domicilio:
  - Puede agregar o eliminar Residentes Secundarios.
  - Puede modificar su información o revocar su acceso en cualquier momento.
- Los **ResidentSecondary** tienen permisos limitados:
  - Invitar visitantes.
  - Visualizar sus accesos.
  - Recibir notificaciones personales.
- Todas las acciones de los usuarios deben estar **registradas y auditables**, desde la creación de invitaciones hasta intentos de acceso.

La revocación de acceso es inmediata: si un usuario es eliminado, pierde acceso a la app, sus invitaciones activas son canceladas, y se impide cualquier acceso físico con sus credenciales.

Este modelo promueve autonomía dentro de cada casa y fraccionamiento, sin perder la trazabilidad ni comprometer la seguridad operativa del sistema.

