# Bounded Contexts (Etapa 1 – Conceptual)

Este documento presenta los **bounded contexts conceptuales** identificados para AxolPass.  
Cada contexto representa un **límite natural del dominio**, donde viven conceptos, reglas y procesos del negocio.

---

## Invitation Management  
**Propósito:**  
Gestionar el ciclo de vida de las invitaciones que los residentes generan para permitir el acceso de visitantes.

**Conceptos del negocio:**  
- Invitación  
- Visitante  
- Residente  
- Vigencia y restricciones  

**Reglas del negocio:**  
- Una invitación pertenece a una vivienda.  
- Una invitación puede ser cancelada por el residente que la generó.  
- Las invitaciones tienen vigencia y restricciones definidas por la comunidad.  

**Actores:**  
- ResidentMain  
- ResidentSecondary  
- Guest  

**Dependencias conceptuales:**  
- Requiere identidad del residente.  
- Requiere reglas de acceso de la comunidad.

---

## Access Control  
**Propósito:**  
Tomar decisiones de acceso basadas en invitaciones, reglas y estado operativo de la comunidad.

**Conceptos del negocio:**  
- Evento de acceso  
- Validación de invitación  
- Estado de puertas o zonas  
- Disponibilidad de estacionamiento  

**Reglas del negocio:**  
- Un acceso debe ser autorizado o denegado según reglas de la comunidad.  
- Un acceso puede depender de la vigencia de una invitación.  
- El sistema debe registrar cada intento de acceso.  

**Actores:**  
- SecurityGuard  
- Guest  

**Dependencias conceptuales:**  
- Requiere invitaciones válidas (Invitation Management).  
- Requiere reglas de acceso (Rule Engine).  
- Requiere configuración de comunidad (Community Configuration).  
- Publica eventos de acceso para auditoría.

---

## Community Configuration  
**Propósito:**  
Definir la estructura operativa del fraccionamiento: zonas, puertas, horarios y reglas generales.

**Conceptos del negocio:**  
- Zona  
- Puerta  
- Regla de acceso  
- Horarios  

**Reglas del negocio:**  
- Las reglas deben ser coherentes con la estructura física del fraccionamiento.  
- Las configuraciones afectan cómo se evalúan los accesos.  

**Actores:**  
- CommunityAdmin  

**Dependencias conceptuales:**  
- Provee reglas y configuración a Access Control.  
- Provee estructura de viviendas a Housing Administration.

---

## Housing Administration  
**Propósito:**  
Administrar residentes, viviendas y permisos operativos asociados a cada unidad privativa.

**Conceptos del negocio:**  
- Residente  
- Vivienda  
- Permisos operativos  

**Reglas del negocio:**  
- Un residente principal puede delegar permisos a residentes secundarios.  
- Una vivienda puede ser suspendida por morosidad interna.  

**Actores:**  
- CommunityAdmin  
- ResidentMain  

**Dependencias conceptuales:**  
- Requiere identidad de usuarios (User Management).  
- Provee información de permisos a Access Control.

---

## Rule Engine  
**Propósito:**  
Gestionar reglas del negocio que afectan accesos y comportamiento operativo del fraccionamiento.

**Conceptos del negocio:**  
- Regla  
- Condición  
- Resultado  

**Reglas del negocio:**  
- Las reglas pueden afectar accesos, suspensiones o restricciones.  
- Las reglas deben ser consistentes con la configuración de la comunidad.  

**Actores:**  
- CommunityAdmin  
- SystemAdmin  

**Dependencias conceptuales:**  
- Provee reglas a Access Control.  
- Recibe configuración desde Community Configuration.

---

## Notification Service  
**Propósito:**  
Comunicar eventos relevantes a residentes y visitantes (invitaciones, accesos, alertas).

**Conceptos del negocio:**  
- Notificación  
- Plantilla  
- Destinatario  

**Reglas del negocio:**  
- Las notificaciones deben enviarse cuando ocurren eventos relevantes.  
- Las notificaciones deben respetar la privacidad del residente.  

**Actores:**  
- ResidentMain  
- CommunityAdmin  

**Dependencias conceptuales:**  
- Recibe eventos desde Invitation Management y Access Control.

---

## Access Monitoring & Reports  
**Propósito:**  
Registrar eventos de auditoría y generar reportes operativos para la comunidad.

**Conceptos del negocio:**  
- Evento de auditoría  
- Métrica  
- Reporte  

**Reglas del negocio:**  
- Todos los accesos deben ser auditables.  
- Los reportes deben reflejar la actividad real del fraccionamiento.  

**Actores:**  
- CommunityAdmin  
- SystemAdmin  

**Dependencias conceptuales:**  
- Recibe eventos desde Access Control, Guard Management y Fallback & Recovery.

---

## Fallback & Recovery  
**Propósito:**  
Gestionar accesos manuales y registros de contingencia cuando el flujo normal no puede operar.

**Conceptos del negocio:**  
- Acceso manual  
- Incidencia  
- Registro de contingencia  

**Reglas del negocio:**  
- Los accesos manuales deben ser auditados.  
- Las incidencias deben registrarse para análisis posterior.  

**Actores:**  
- SecurityGuard  

**Dependencias conceptuales:**  
- Publica incidencias a Access Monitoring & Reports.

---

## Guard Management  
**Propósito:**  
Administrar guardias, sus permisos y sus intervenciones operativas.

**Conceptos del negocio:**  
- Guardia  
- Intervención  
- Registro manual  

**Reglas del negocio:**  
- Cada intervención debe quedar registrada.  
- Los guardias deben tener permisos definidos por la comunidad.  

**Actores:**  
- SecurityGuard  
- CommunityAdmin  

**Dependencias conceptuales:**  
- Publica registros a Access Monitoring & Reports.

---

## User Management  
**Propósito:**  
Gestionar la identidad digital de los usuarios del sistema.

**Conceptos del negocio:**  
- Usuario  
- Rol  
- Permiso  

**Reglas del negocio:**  
- Un usuario puede tener roles distintos según su comunidad.  
- La identidad debe ser única y consistente.  

**Actores:**  
- SystemAdmin  
- CommunityAdmin  

**Dependencias conceptuales:**  
- Provee identidad a todos los demás contextos.

---

## Axolote Administration  
**Propósito:**  
Gestionar clientes, fraccionamientos y su estado operativo general.

**Conceptos del negocio:**  
- Comunidad  
- Plan  
- Estado operativo  

**Reglas del negocio:**  
- Una comunidad debe tener un administrador asignado.  
- Una comunidad puede ser suspendida por morosidad global.  

**Actores:**  
- SystemAdmin  

**Dependencias conceptuales:**  
- Provee comunidades y administradores a User Management y Community Configuration.
