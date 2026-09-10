# 🧩 Bloque 14: Configuración General del Fraccionamiento

## 🎤 Entrevistas Simuladas (Stakeholders y Analista)

---

### 👩‍💼 CommunityAdmin – Administrador del Fraccionamiento

**Analista:** ¿Qué elementos necesitas poder configurar desde tu panel?  
**CommunityAdmin:** Principalmente necesito dar de alta o modificar los datos del fraccionamiento: nombre, dirección, número de casas, y definir qué servicios contratamos (por ejemplo, control de acceso, pagos, etc.).

**Analista:** ¿Quién define las reglas como número de usuarios por casa o los nombres de calles?  
**CommunityAdmin:** Nosotros. Cada fraccionamiento tiene su propia estructura. Algunos tienen calles con nombre, otros no. También necesitamos definir cuántos usuarios puede haber por casa, y si permitimos residentes secundarios.

**Analista:** ¿Puedes modificar las casas desde el panel?  
**CommunityAdmin:** Sí, necesito poder activar, suspender o reactivar casas, cambiar su número, asignarles calle, etc. También poder eliminar usuarios si se mudan o terminan contrato.

**Analista:** ¿Qué pasa si hay una casa nueva o una división?  
**CommunityAdmin:** Quiero poder agregar casas nuevas sin necesidad de contactar a soporte. Si hay un cambio fuerte, como dividir un lote, tal vez sí necesitemos asistencia.

---

### 🛠 SystemAdmin – Axolote Solutions

**Analista:** ¿Cómo se gestiona hoy la configuración general de cada fraccionamiento?  
**SystemAdmin:** En la mayoría de los casos, la hacemos nosotros manualmente. Pero queremos migrar eso a una configuración editable desde el panel del CommunityAdmin.

**Analista:** ¿Hay configuraciones globales que deban respetarse?  
**SystemAdmin:** Sí, algunas cosas como la validez del QR o el diseño del flujo de acceso no deberían cambiar. Pero otras, como el número de usuarios por casa, sí pueden personalizarse.

**Analista:** ¿Qué más deberían poder modificar los CommunityAdmins?  
**SystemAdmin:** Elementos como: nombre de calles internas, si usan nomenclatura por lotes, número de casas, asignación de usuarios, suspensión de servicios, etc.

---

## 📝 Notas de Campo del Analista

- Cada fraccionamiento tiene su propia estructura: cantidad de casas, calles, nombres, políticas internas.
- CommunityAdmin debe poder:
  - Crear y editar casas.
  - Suspender o reactivar una casa (por impago, mudanza, etc.).
  - Eliminar usuarios de una casa (ej. renta vencida).
  - Configurar nombre del fraccionamiento, dirección, nomenclatura interna.
  - Definir reglas como número máximo de usuarios por casa.
- La idea es ofrecer autonomía sin romper reglas globales definidas por Axolote.
- La interfaz debe ser clara y accesible, sin conocimientos técnicos.

---

## 📚 Narrativa Funcional Extendida

El módulo de **Configuración General del Fraccionamiento** habilita a los **CommunityAdmins** para gestionar la estructura interna y las políticas básicas de su comunidad, sin requerir intervención directa de Axolote Solutions.

Desde un panel administrativo, el administrador podrá:

- **Modificar datos generales del fraccionamiento**: nombre, dirección, número total de casas, etc.
- **Definir la estructura interna**: nombres de calles (si aplica), agrupaciones por manzanas, nomenclatura de lotes.
- **Gestionar casas individuales**:
  - Crear una nueva casa o lote.
  - Editar datos existentes.
  - Suspender o reactivar servicios para una casa en particular.
  - Eliminar usuarios cuando ya no pertenecen a la vivienda (por ejemplo, término de arrendamiento).

Adicionalmente, podrá **configurar parámetros de control** como:
- Número máximo de usuarios por casa.
- Permitir o no usuarios secundarios.
- Reglas de asignación por casa o calle.

Este módulo busca un equilibrio entre flexibilidad y control, permitiendo que cada fraccionamiento mantenga su autonomía y estructura única, sin afectar los lineamientos globales del sistema AxolPass.

Las funcionalidades estarán disponibles mediante una **interfaz web sencilla e intuitiva**, con validaciones automáticas para prevenir errores estructurales.

