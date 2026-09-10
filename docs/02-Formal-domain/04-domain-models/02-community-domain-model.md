# Community domain model

## 2. Contexto de Gestión de Comunidad

**Propósito:** Administra el espacio físico, la asignación de propiedades a personas y las reglas globales operativas.

### **Entidades del Dominio:**
* **Comunidad:** El cliente organizacional y el recinto geográfico.

* **Unidad Privativa (Casa):** El espacio físico individual asignable.

* **Vínculo de Residencia:** El contrato lógico que liga a un individuo con una propiedad.

* **Modelo de Propiedad:** El arquetipo arquitectónico del cual las casas heredan características.


### **Value Objects Conceptuales:**
* **Configuración de Comunidad:** Las reglas del juego globales, como el aforo total y la zona horaria.

* **Estado Operativo:** El semáforo de derechos (Activo o Suspendido) de una unidad privativa.


### **Invariantes del Negocio:**
* Debe existir una unicidad espacial estricta; no pueden existir dos casas con la misma nomenclatura.

* El total de casas registradas no puede superar la capacidad máxima estipulada en el contrato de suscripción.

* Los aforos configurados deben ser siempre números positivos.


### **Reglas Internas del Contexto:**
* Una casa recién registrada nace operativa y vacía.

* Si se remueve al titular (residente principal) de una casa, todos sus residentes secundarios caen en cascada.

* Vulnerar la privacidad anonimizada de la comunidad requiere un flujo de excepción (Auditoría Forense) con justificación obligatoria.


### **Estados y Transiciones:**
* *Comunidad:* `PENDING_SETUP` $\rightarrow$ `ACTIVE` (al configurar reglas).

* *Unidad Privativa:* `ACTIVE` $\leftrightarrow$ `SUSPENDED` (por sanción administrativa).


### **Relaciones entre Entidades:**
* La *Comunidad* contiene múltiples *Modelos de Propiedad* y *Unidades Privativas*.

* La *Unidad Privativa* gobierna a sus *Vínculos de Residencia*.


### **Límites del Agregado (Candidatos):**
* **Agregado Raíz Global:** *Comunidad* (Maneja las configuraciones globales).

* **Agregado Raíz Local:** *Unidad Privativa* (Garantiza la consistencia de sus residentes y su estado).


### **Lenguaje Ubicuo del Contexto:**
* Community, House, HouseModel, Tenancy, CommunitySettings, OperationalStatus, Break-Glass, ResidentTransfer.



