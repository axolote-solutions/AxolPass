# 🧩 Bloque 8: Administración y Configuración General

---

## 🎤 Entrevistas Simuladas (Stakeholders y Analista)

### 👨‍💼 CommunityAdmin

**Analista:** ¿Qué tipo de configuraciones espera poder realizar desde su panel de administración?  
**CommunityAdmin:** Deberíamos poder ver la lista de casas, agregar nuevas, cambiar su estatus (activa, suspendida), y asociar o eliminar usuarios de cada una.

**Analista:** ¿Quién puede suspender una casa o quitar usuarios?  
**CommunityAdmin:** Solo nosotros los administradores del fraccionamiento. Por ejemplo, si termina un contrato de renta, se eliminan los usuarios vinculados.

**Analista:** ¿Qué otros elementos del fraccionamiento debería poder administrar?  
**CommunityAdmin:** También sería útil actualizar el nombre, el reglamento, el número de casas, y cargar imágenes si la app algún día lo permite. Tal vez incluso un logo por fraccionamiento.

---

### 👨‍💼 SystemAdmin

**Analista:** ¿Cómo se gestionan los fraccionamientos desde Axolote Solutions?  
**SystemAdmin:** Nosotros creamos y activamos fraccionamientos. Podemos modificar la información general, suspenderlos temporalmente o eliminarlos si es necesario, pero toda acción crítica debe quedar registrada con justificación.

**Analista:** ¿Pueden los CommunityAdmins crear fraccionamientos?  
**SystemAdmin:** No. Solo nosotros. Pero una vez creado, ellos se encargan de su operación interna.

**Analista:** ¿Qué información se configura al registrar un fraccionamiento?  
**SystemAdmin:** Nombre, ubicación, número de casas, administrador asignado, tal vez reglas locales, y en el futuro podríamos permitir personalización visual mínima (logo, colores).

---

## 📝 Notas de Campo del Analista

- El CommunityAdmin tiene permisos de:
  - Agregar, editar y suspender casas.
  - Asociar/desasociar usuarios.
  - Ver y actualizar datos del fraccionamiento (nombre, reglamento, etc.)
- Solo el SystemAdmin puede crear o eliminar fraccionamientos.
- Las acciones críticas como suspensiones o eliminaciones deben **guardar justificación**.
- Se prevé que en el futuro haya **personalización visual por fraccionamiento**, pero por ahora el diseño será estándar.
- La visibilidad del CommunityAdmin se limita a su propio fraccionamiento.
- No se permite crear casas desde la app móvil (solo desde el panel web de administración).

---

## 📚 Narrativa Funcional Extendida

El módulo de **Administración y Configuración General** permite a los actores clave del sistema gestionar las entidades base: fraccionamientos, casas y usuarios.

En la jerarquía de permisos:

- **SystemAdmin** (Axolote Solutions) tiene control total del sistema. Puede:
  - Crear nuevos fraccionamientos.
  - Editar su información general (nombre, ubicación, número de casas).
  - Suspender temporalmente fraccionamientos o eliminarlos por completo, siempre dejando **registro de justificación**.
  - Asignar a un CommunityAdmin como responsable operativo.

- **CommunityAdmin** tiene autoridad dentro de su fraccionamiento:
  - Puede crear y editar casas, cambiar su estatus (activa, suspendida).
  - Asociar y eliminar usuarios de cada casa.
  - Visualizar historial de accesos y estadísticas.
  - Configurar elementos como el reglamento o nombre del fraccionamiento.

El diseño del sistema **no permite delegación ni subroles**. Todas las acciones administrativas están trazadas para garantizar transparencia y control.

En fases futuras, se contempla habilitar una **capa ligera de personalización** para los fraccionamientos (logos, nombres visibles en la app, etc.), pero en la versión inicial, todos comparten una experiencia visual estándar.

Este módulo asegura que cada fraccionamiento se mantenga actualizado, con control sobre su estructura de viviendas, sin perder la trazabilidad ni comprometer la seguridad operativa.
