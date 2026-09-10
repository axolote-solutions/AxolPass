# Etapa 1 – Análisis de Dominio (DDD)

## Objetivo
La Etapa 1 tiene como propósito **comprender profundamente el dominio del negocio**, identificar sus conceptos fundamentales, sus reglas, sus límites naturales y su lenguaje común.  
Esta etapa establece la base conceptual que permitirá diseñar modelos expresivos y arquitecturas coherentes en etapas posteriores.

La Etapa 1 es **analítica**, **conceptual**, **colaborativa** y **100% independiente de tecnología**.

---

# Qué es la Etapa 1
La Etapa 1 consiste en:

- entender el dominio  
- mapear conceptos  
- identificar límites naturales (bounded contexts)  
- descubrir reglas del negocio  
- definir el lenguaje ubicuo  
- documentar procesos del negocio (no flujos técnicos)  
- identificar entidades, agregados y value objects conceptuales  

No se diseña arquitectura.  
No se escribe código.  
No se definen microservicios.  
No se selecciona tecnología.

---

# Qué se debe obtener de la Etapa 1

## 1. **Lenguaje Ubicuo**
Debe quedar documentado un glosario compartido entre:

- Axolote Solutions  
- Mesa Directiva  
- CommunityAdmin  
- Residentes  
- Guardias  
- Equipo técnico  

El lenguaje ubicuo incluye:

- términos del negocio  
- definiciones  
- reglas asociadas  
- ejemplos  
- anti‑ejemplos  

El lenguaje ubicuo es **vivo** y se refina durante toda la etapa.

---

## 2. **Mapa de Dominio**
Documento que describe:

- conceptos principales  
- relaciones entre conceptos  
- dependencias naturales  
- áreas funcionales del negocio  

Este mapa NO es técnico.  
Es un mapa conceptual del negocio.

---

## 3. **Bounded Contexts (conceptuales)**
Identificación de los límites naturales del dominio, por ejemplo:

- Suscripción y Facturación  
- Comunidad  
- Accesos  
- Identidad  
- Notificaciones  

Cada bounded context debe incluir:

- propósito  
- conceptos principales  
- reglas del negocio  
- actores involucrados  
- dependencias con otros contextos  

No se define arquitectura hexagonal aquí.  
Solo los límites conceptuales del dominio.

---

## 4. **Modelos del Dominio (conceptuales)**
Identificación de:

- entidades  
- agregados  
- value objects  
- reglas invariantes  
- políticas del negocio  
- restricciones naturales  

Estos modelos NO incluyen:

- bases de datos  
- tablas  
- endpoints  
- clases  
- código  
- microservicios  

Son modelos conceptuales del negocio.

---

## 5. **Procesos del Negocio (no técnicos)**
Documentación de procesos como:

- cómo se registra un residente  
- cómo se genera un pase  
- cómo se valida un acceso  
- cómo se suspende una casa  
- cómo se aplica morosidad  
- cómo se gestiona un visitante sorpresa  

Estos procesos NO son diagramas técnicos.  
Son descripciones del negocio en lenguaje natural.

---

## 6. **Reglas del Negocio**
Cada regla debe documentarse con:

- descripción  
- propósito  
- actores involucrados  
- restricciones  
- excepciones  
- ejemplos  

Las reglas del negocio son la base de los futuros agregados.

---

## 7. **Eventos del Dominio (conceptuales)**
Identificación de hechos relevantes del negocio, por ejemplo:

- InvitaciónGenerada  
- InvitaciónCancelada  
- AccesoValidado  
- AccesoDenegado  
- CasaSuspendida  
- PagoRegistrado  

Los eventos NO se modelan técnicamente.  
Solo se identifican conceptualmente.

---

# Qué preguntas guía esta etapa

### Lenguaje ubicuo
- ¿Qué palabras usa el negocio?  
- ¿Qué significan realmente?  
- ¿Qué términos se confunden?  
- ¿Qué términos deben eliminarse?

### Bounded contexts
- ¿Qué áreas del negocio son independientes?  
- ¿Qué áreas dependen unas de otras?  
- ¿Qué límites naturales existen?

### Modelos del dominio
- ¿Qué conceptos tienen identidad?  
- ¿Qué conceptos son valores?  
- ¿Qué reglas nunca deben romperse?  
- ¿Qué invariantes existen?

### Procesos del negocio
- ¿Qué hace el negocio hoy?  
- ¿Qué debería hacer?  
- ¿Qué pasos son obligatorios?  
- ¿Qué excepciones existen?

### Eventos del dominio
- ¿Qué hechos relevantes ocurren?  
- ¿Qué cambios de estado son importantes?  
- ¿Qué eventos deben ser auditables?

---

# Artefactos generados en la Etapa 1

### 1. **domain-overview.md**
Resumen del dominio y sus áreas principales.

### 2. **ubiquitous-language.md**
Glosario vivo del negocio.

### 3. **domain-map.md**
Mapa conceptual del dominio.

### 4. **bounded-contexts.md**
Lista y definición de los bounded contexts conceptuales.

### 5. **domain-models.md**
Entidades, agregados y value objects conceptuales.

### 6. **business-processes.md**
Procesos del negocio en lenguaje natural.

### 7. **domain-events.md**
Lista de eventos del dominio (conceptuales).

---

# Qué NO debe ocurrir en la Etapa 1

Para mantener la disciplina del proceso, la Etapa 1 **no debe incluir**:

- arquitectura hexagonal  
- microservicios  
- bases de datos  
- tablas  
- endpoints  
- clases  
- código  
- diagramas técnicos  
- decisiones de infraestructura  
- decisiones de tecnología  
- patrones técnicos  
- ADRs técnicos  

La Etapa 1 es **conceptual**, no técnica.

---

# Criterios de salida de la Etapa 1

La etapa se considera completa cuando:

- el lenguaje ubicuo está definido  
- el mapa del dominio está documentado  
- los bounded contexts están identificados  
- los modelos del dominio están descritos  
- los procesos del negocio están documentados  
- las reglas del negocio están claras  
- los eventos del dominio están identificados  
- todos los artefactos están en `/docs/domain`  

Solo entonces se puede avanzar a la Etapa 2.

