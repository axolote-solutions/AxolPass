# Documentation Structure

## Objetivo
Definir la estructura organizativa de la documentación dentro del monorepo AxolPass.  
Este documento establece **cómo se clasifica, almacena y mantiene** la información del proyecto, garantizando orden, trazabilidad y consistencia durante todas las etapas del proceso.

La estructura aquí definida **no depende de arquitectura**, **no depende de requerimientos**, y **no depende de microservicios**.  
Es un estándar organizativo del proyecto.

---

## Estructura General de Documentación

```
/docs
    /process
    /architecture
    /domain
    /decisions
    /operations
```

Cada subdirectorio tiene un propósito específico y se llena en etapas distintas del proceso.

---

## Descripción de Directorios

### `/docs/process`
Contiene toda la documentación relacionada con **cómo trabajamos**, no con el sistema en sí.

Incluye:
- estructura del repositorio  
- estructura de documentación  
- nomenclaturas  
- SLA/SLO/SLI  
- herramientas del proyecto  
- principios del proceso  
- bitácoras  
- guías de contribución  
- estándares de ramas y PRs  

Este directorio se completa durante el **Paso‑1** y puede crecer conforme se formalicen prácticas del equipo.

---

### `/docs/architecture`
Contiene documentación relacionada con la **arquitectura del sistema**, que se definirá en etapas posteriores.

Incluye (cuando exista):
- visión arquitectónica  
- diagramas de componentes  
- decisiones de estilo arquitectónico  
- estructura de microservicios  
- integración entre servicios  
- modelos de despliegue  
- seguridad y gobernanza técnica  

Este directorio permanece **vacío en el Paso‑1**.

---

### `/docs/domain`
Contiene documentación del **análisis de dominio**, que se desarrolla en la Etapa 1.

Incluye (cuando exista):
- glosario de términos  
- modelos de dominio  
- actores  
- procesos de negocio  
- reglas del dominio  
- bounded contexts (si aplica)  

Este directorio permanece **vacío en el Paso‑1**.

---

### `/docs/decisions`
Contiene los **Architecture Decision Records (ADR)** y cualquier decisión técnica formal tomada durante el proyecto.

Incluye:
- ADR numerados  
- decisiones de arquitectura  
- decisiones de infraestructura  
- decisiones de seguridad  
- decisiones de integración  

Este directorio permanece **vacío en el Paso‑1**.

---

### `/docs/operations`
Contiene documentación relacionada con la **operación del sistema**, que se desarrolla en etapas posteriores.

Incluye (cuando exista):
- despliegue  
- monitoreo  
- observabilidad  
- alertas  
- procedimientos de recuperación  
- manuales de operación  

Este directorio permanece **vacío en el Paso‑1**.

---

## Consideraciones

- Toda documentación vive dentro del monorepo para asegurar trazabilidad y versionado.  
- La documentación se organiza por **temas**, no por servicios ni por arquitectura.  
- Ningún documento técnico debe colocarse en `/docs/process`.  
- Los directorios vacíos son intencionales y se llenarán conforme avance el proceso.  
- La estructura puede ampliarse mediante ADRs cuando sea necesario.

---

## Estado Actual
Este documento forma parte del **Paso‑1** y establece únicamente la organización de la documentación.  
Los directorios `/architecture`, `/domain`, `/decisions` y `/operations` se completarán en etapas posteriores del proceso.

