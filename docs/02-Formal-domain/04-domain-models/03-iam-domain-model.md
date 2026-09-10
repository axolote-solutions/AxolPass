## IAM domain model

## 3. Contexto de Identidad y Accesos (IAM)

**Propósito:** Frontera única de confianza que orquesta la autenticación delegada, las cuentas sombra y los privilegios operativos.

### **Entidades del Dominio:**
* **Usuario Global:** La representación única y universal de una persona en todo el ecosistema.

* **Alcance Operativo (Scope):** El límite de jurisdicción que tiene un trabajador (ej. guardia) sobre un fraccionamiento.

### **Value Objects Conceptuales:**
* **Estado de Cuenta:** El momento de vida de la cuenta dentro del sistema.

* **Identificador Externo:** La referencia delegada hacia el proveedor de identidad.

* **Rol Operativo:** La jerarquía de autoridad (ej. Admin, Guardia).

### **Invariantes del Negocio:**
* La plataforma no guarda, conoce ni gestiona contraseñas; asume como válida la identidad verificada por el proveedor externo.

* Los roles operativos están fuertemente acotados; un guardia no tiene autoridad fuera de la comunidad que se le asignó.

### **Reglas Internas del Contexto:**
* El sistema asocia identidades para no duplicar datos personales en otros módulos (Patrón Cuenta Sombra).

* Ciertas operaciones críticas (como presionar el botón de pánico físico) ignoran la validación de sesión para proteger la vida.

### **Estados y Transiciones (Cuenta de Usuario):**
* `PENDING_ACTIVATION` $\rightarrow$ `ACTIVE` $\leftrightarrow$ `SUSPENDED`.

* *Cualquier Estado* $\rightarrow$ `DISABLED` (Baja definitiva).

### **Relaciones entre Entidades:**
* Un *Usuario Global* posee uno o múltiples *Alcances Operativos* dependiendo de sus roles.

### **Límites del Agregado (Candidatos):**
* **Agregado Raíz:** *Usuario Global* (Maneja sus identificadores y alcances).

### **Lenguaje Ubicuo del Contexto:**
* globalUserId, AccountStatus, CommunityScope, IdentityProvider, SystemAdmin, CommunityAdmin, SecurityGuard.


