# domain-map.md  
## Mapa de Dominio – AxolPass (Etapa 1)

---

## 1. Propósito del documento  
El **Domain Map** describe las **áreas funcionales del dominio AxolPass**, los **conceptos principales** que viven en cada área, las **relaciones naturales** entre ellos y los **límites conceptuales** del negocio.  
Este documento sirve como base para:

- construir el **lenguaje ubicuo**  
- identificar los **bounded contexts conceptuales**  
- preparar el terreno para el diseño del dominio en etapas posteriores  

El domain map es **conceptual**, no técnico.

---

## 2. Áreas funcionales del dominio

Estas son las **macro‑zonas del negocio**, donde viven los conceptos y reglas naturales del sistema AxolPass.

---

### Área: Accesos y Control Operativo  
**Descripción:**  
Gestiona todo lo relacionado con la entrada y salida de personas al fraccionamiento, incluyendo validación de invitaciones, decisiones de acceso y registro de eventos.

**Problemas que resuelve:**  
- Control seguro de accesos  
- Validación de visitantes  
- Registro de actividad operativa  

**Actores:**  
- SecurityGuard  
- ResidentMain  
- ResidentSecondary  
- Guest  

**Conceptos que viven aquí:**  
- Acceso  
- Invitación  
- Visitante  
- Guardia  
- Evento de acceso  

---

### Área: Gestión de Comunidad  
**Descripción:**  
Administra la estructura física y organizacional del fraccionamiento: viviendas, residentes, zonas, puertas y reglas internas.

**Problemas que resuelve:**  
- Organización de viviendas  
- Administración de residentes  
- Configuración operativa de la comunidad  

**Actores:**  
- CommunityAdmin  
- ResidentMain  

**Conceptos que viven aquí:**  
- Comunidad  
- Vivienda  
- Residente  
- Zona  
- Puerta  

---

### Área: Identidad y Usuarios  
**Descripción:**  
Gestiona la identidad digital de todos los usuarios del sistema, sus roles y permisos.

**Problemas que resuelve:**  
- Identificación de usuarios  
- Roles y permisos  
- Autorización conceptual  

**Actores:**  
- SystemAdmin  
- CommunityAdmin  
- ResidentMain  
- SecurityGuard  

**Conceptos que viven aquí:**  
- Usuario  
- Rol  
- Permiso  

---

### Área: Reglas y Políticas  
**Descripción:**  
Define y administra las reglas del negocio que afectan accesos, suspensiones, restricciones y comportamiento operativo.

**Problemas que resuelve:**  
- Políticas de acceso  
- Suspensiones por morosidad  
- Condiciones operativas  

**Actores:**  
- CommunityAdmin  
- SystemAdmin  

**Conceptos que viven aquí:**  
- Regla  
- Condición  
- Política  

---

### Área: Notificaciones y Comunicación  
**Descripción:**  
Gestiona la comunicación hacia residentes y visitantes sobre eventos relevantes del sistema.

**Problemas que resuelve:**  
- Envío de invitaciones  
- Alertas de acceso  
- Comunicación operativa  

**Actores:**  
- ResidentMain  
- CommunityAdmin  

**Conceptos que viven aquí:**  
- Notificación  
- Mensaje  
- Destinatario  

---

### Área: Auditoría y Observabilidad  
**Descripción:**  
Registra eventos relevantes del sistema y permite generar reportes operativos y de seguridad.

**Problemas que resuelve:**  
- Auditoría de accesos  
- Registro de incidentes  
- Reportes operativos  

**Actores:**  
- SystemAdmin  
- CommunityAdmin  

**Conceptos que viven aquí:**  
- Evento de auditoría  
- Métrica  
- Reporte  

---

### Área: Administración Comercial (Axolote)  
**Descripción:**  
Gestiona la relación comercial con los fraccionamientos, su alta, estado operativo y administración global.

**Problemas que resuelve:**  
- Alta de comunidades  
- Asignación de administradores  
- Estado operativo global  

**Actores:**  
- SystemAdmin  

**Conceptos que viven aquí:**  
- Comunidad (alta)  
- Plan  
- Estado operativo  

---

## 3. Conceptos principales del dominio

Cada concepto del negocio debe tener una definición clara dentro del **lenguaje ubicuo**.

- **Invitación:** Permiso temporal generado por un residente para permitir el acceso de un visitante.  
- **Visitante:** Persona externa que requiere ingresar al fraccionamiento.  
- **Residente:** Persona asociada a una vivienda, con permisos operativos.  
- **Vivienda:** Unidad privativa dentro de una comunidad.  
- **Acceso:** Evento que representa la entrada o salida de una persona.  
- **Regla:** Condición que afecta accesos o comportamiento operativo.  
- **Comunidad:** Fraccionamiento o condominio administrado por AxolPass.  
- **Guardia:** Persona encargada de operar la caseta y validar accesos.  
- **Notificación:** Mensaje enviado a un usuario sobre un evento relevante.  
- **Evento de auditoría:** Registro de un hecho relevante del sistema.  

Cada concepto se documenta en detalle en:  
👉 **ubiquitous-language.md**

---

## 4. Relaciones conceptuales entre áreas

Estas relaciones describen **dependencias naturales del negocio**, sin flechas, sin arquitectura y sin patrones técnicos.

- Las invitaciones dependen de la identidad del residente.  
- Los accesos dependen de las invitaciones y de las reglas de la comunidad.  
- Las reglas dependen de la configuración de la comunidad.  
- Las notificaciones dependen de eventos del dominio (invitaciones, accesos).  
- La auditoría recibe eventos de todas las áreas.  
- La administración comercial crea comunidades y asigna administradores.  

---

## 5. Reglas del negocio por área

### Accesos y Control Operativo  
- Todo acceso debe ser autorizado o denegado.  
- Un acceso puede depender de una invitación válida.  
- Todo acceso debe quedar registrado.  

### Gestión de Comunidad  
- Una vivienda pertenece a una comunidad.  
- Un residente principal puede delegar permisos.  
- Una comunidad puede suspender viviendas por morosidad.  

### Identidad y Usuarios  
- Un usuario tiene una identidad única.  
- Los roles determinan permisos operativos.  

### Reglas y Políticas  
- Las reglas afectan accesos y suspensiones.  
- Las reglas deben ser coherentes con la configuración de la comunidad.  

### Notificaciones  
- Las notificaciones deben enviarse cuando ocurren eventos relevantes.  

### Auditoría  
- Todo evento relevante debe ser registrado.  

### Administración Comercial  
- Una comunidad debe tener un administrador asignado.  

---

## 6. Mapa conceptual (diagrama simple)

![alt text](./10-diagrams/domain-map.png)

---

## 7. Límites naturales del dominio (pre‑bounded contexts)

Antes de definir bounded contexts formales, se identifican **zonas conceptuales independientes**:

- Invitaciones  
- Accesos  
- Comunidad  
- Identidad  
- Reglas  
- Notificaciones  
- Auditoría  
- Administración Axolote  

Estos límites conceptuales se transformarán en bounded contexts en:  
👉 **bounded-contexts.md**

