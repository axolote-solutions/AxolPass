# 🧩 Bloque 7: Roles y Control de Acceso

---

## 🎤 Entrevistas Simuladas (Stakeholders y Analista)

### 👨‍💼 SystemAdmin (Axolote Solutions)

**Analista:** ¿Qué roles principales están previstos en el sistema?  
**SystemAdmin:** Tenemos definidos los siguientes:

- **SystemAdmin:** Personal de Axolote Solutions con acceso global al sistema, administración de fraccionamientos y monitoreo.
- **CommunityAdmin:** Administrador de un fraccionamiento específico. Gestiona casas, usuarios y supervisa accesos.
- **ResidentMain:** Usuario principal de una vivienda. Control total sobre invitaciones y gestión de usuarios secundarios.
- **ResidentSecondary:** Usuarios autorizados por el principal. Pueden generar invitaciones y consultar su historial.
- **SecurityGuard:** Usuario de caseta, con funciones de validación, registro de eventos y operación de barreras.

**Analista:** ¿Qué mecanismos usan para autenticar usuarios?  
**SystemAdmin:** Todos acceden con contraseña. En el futuro, podríamos habilitar biometría y 2FA para roles críticos.

---

### 👮 Guardia de Seguridad

**Analista:** ¿Qué funciones realiza desde el sistema?  
**SecurityGuard:** Validamos códigos QR, registramos accesos manuales, errores de lectura, y abrimos barreras. También podemos consultar el historial del día.

**Analista:** ¿Tienen acceso a datos personales?  
**SecurityGuard:** Solo al nombre y placa del visitante. No tenemos permisos de edición.

---

### 🧑‍💼 CommunityAdmin

**Analista:** ¿Qué nivel de control tiene el administrador de fraccionamiento?  
**CommunityAdmin:** Podemos configurar las casas (crear, editar, suspender), asociar usuarios, y ver estadísticas. También eliminamos usuarios cuando expira una renta, por ejemplo.

**Analista:** ¿Puede haber más de un administrador por fraccionamiento?  
**CommunityAdmin:** No por ahora. Aunque en el futuro sería útil tener un suplente o rol delegado.

**Analista:** ¿Pueden personalizarse permisos por fraccionamiento?  
**CommunityAdmin:** Hoy todo es estándar. Tal vez a futuro, podríamos tener configuraciones especiales por comunidad.

---

## 📝 Notas de Campo del Analista

- Se identificaron cinco roles principales con **niveles de acceso diferenciados**.
- El **CommunityAdmin** puede suspender casas, eliminar usuarios de viviendas y gestionar accesos.
- **Usuarios secundarios** pueden crear invitaciones pero tienen visibilidad limitada.
- **Guardias** tienen solo lectura sobre invitaciones activas y acceso al módulo de validación.
- Actualmente no hay soporte para:
  - Delegación de funciones
  - Subroles
  - Configuraciones personalizadas por fraccionamiento
- La **autenticación es por contraseña**, con posible 2FA en el futuro.
- El **SystemAdmin** puede desactivar fraccionamientos, pero debe justificar cada acción.

---

## 📚 Narrativa Funcional Extendida

El sistema AxolPass está estructurado sobre una base de **control de acceso basado en roles (RBAC)**. Cada usuario pertenece a un rol claramente definido, y sus permisos están alineados con las necesidades operativas del fraccionamiento y de la plataforma Axolote Solutions.

Los roles identificados son:

- **SystemAdmin:** Personal de Axolote. Tiene visibilidad global y puede administrar la creación, edición o suspensión de fraccionamientos, así como monitorear el comportamiento general del sistema. Cualquier acción crítica (como desactivar un fraccionamiento) requiere **justificación registrada**.

- **CommunityAdmin:** Responsable operativo del fraccionamiento. Administra el catálogo de casas, puede **suspender servicios por vivienda** (por ejemplo, por falta de pago) y **eliminar usuarios** cuando termina una relación de renta. Tiene acceso al historial de accesos, validaciones manuales del guardia, y puede auditar comportamientos inusuales.

- **ResidentMain:** Usuario principal de una casa. Puede autorizar o eliminar **usuarios secundarios**, generar invitaciones, y consultar el historial completo de accesos e invitaciones de su hogar.

- **ResidentSecondary:** Usuarios autorizados por el principal. Pueden generar sus propias invitaciones y recibir notificaciones, pero no pueden gestionar a otros usuarios ni ver eventos ajenos.

- **SecurityGuard:** Rol operativo en caseta de acceso. Puede validar códigos QR, registrar errores, ingresar datos manuales y acceder a un historial básico del día. Su acceso es de solo lectura, sin posibilidad de modificar invitaciones o usuarios.

Actualmente, el sistema mantiene una estructura **estándar y centralizada**. No se permiten configuraciones distintas por fraccionamiento, ni la creación de subroles o delegaciones. Sin embargo, se contempla esta posibilidad en fases futuras del proyecto.

La **autenticación actual es basada en credenciales**, y se prevé implementar mecanismos como **autenticación de dos factores (2FA)**, especialmente para roles como CommunityAdmin y SystemAdmin, con el fin de aumentar la seguridad de operaciones críticas.

Este módulo es esencial para garantizar que cada acción dentro del sistema esté controlada, trazada y ejecutada solo por los usuarios apropiados, bajo una política clara de **mínimo privilegio** y **responsabilidad individual**.
