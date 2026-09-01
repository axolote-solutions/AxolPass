# Repository Structure (Monorepo)

## Objetivo
Definir la estructura organizativa del monorepo AxolPass para garantizar orden, consistencia y trazabilidad en todas las etapas del proyecto.  
Este documento **no define arquitectura de software**, **no define estructura de código**, y **no define microservicios**.  
Únicamente establece **cómo se organiza el repositorio** en su estado inicial.

---

## Estructura General del Monorepo

```
/docs
    /process
    /architecture
    /domain
    /decisions
    /operations

/services
    (vacío hasta definir arquitectura y microservicios)

/ui
    (vacío hasta definir framework y arquitectura de la interfaz)

/infrastructure
    (vacío hasta definir despliegue, IaC y configuración)

/scripts
README.md
```

---

## Descripción de Directorios

### `/docs`
Contiene **toda la documentación del proyecto**, organizada en subdirectorios para mantener claridad y evitar mezclar documentación con código.

- `/process`  
  Documentos relacionados con el proceso de desarrollo, estándares, nomenclaturas, SLA/SLO/SLI, herramientas y lineamientos generales.

- `/architecture`  
  Se llenará cuando se defina la arquitectura de la solución (Etapa 3).

- `/domain`  
  Se llenará durante el análisis de dominio (Etapa 1).

- `/decisions`  
  Contendrá los ADR (Architecture Decision Records) generados en etapas posteriores.

- `/operations`  
  Documentación relacionada con despliegue, monitoreo, observabilidad y operación del sistema.

---

### `/services`
Directorio reservado para los futuros microservicios del backend.  
Permanece vacío hasta que se definan:

- arquitectura  
- bounded contexts  
- APIs  
- eventos  
- bases de datos  

---

### `/ui`
Directorio reservado para la interfaz de usuario del proyecto AxolPass.  
Permanece vacío hasta que se defina:

- framework (React, Angular, Vue, Svelte, etc.)  
- arquitectura de frontend  
- estructura de componentes  
- estrategia de build y despliegue  

La inclusión de `/ui` en el monorepo permite:

- trazabilidad entre backend y frontend  
- documentación unificada  
- CI/CD centralizado  
- gobernanza consistente  

---

### `/infrastructure`
Directorio reservado para la infraestructura del proyecto:

- Kubernetes manifests  
- Helm/Kustomize  
- Terraform  
- Configuración de AWS  
- Pipelines de CI/CD (si se almacenan aquí)

Permanece vacío hasta las etapas de despliegue.

---

### `/scripts`
Scripts utilitarios para tareas repetitivas:

- automatización  
- validaciones  
- herramientas internas  
- generación de artefactos  

---

### `README.md`
Documento principal del repositorio:

- propósito del proyecto  
- estructura general  
- enlaces a documentación relevante  
- instrucciones iniciales  

---

## Consideraciones

- Toda documentación vive dentro del monorepo para asegurar trazabilidad y versionado.  
- No se define estructura de código en esta etapa.  
- No se definen microservicios ni carpetas técnicas hasta que la arquitectura esté formalmente establecida.  
- La estructura puede ampliarse en etapas posteriores mediante ADRs.  
- La carpeta `/ui` se incluye desde el inicio para evitar repos adicionales y mantener gobernanza centralizada.

---

## Estado Actual
Este documento forma parte del **Paso -1** del proceso y establece únicamente la organización del repositorio.  
Las carpetas `/services`, `/ui` y `/infrastructure` se completarán en etapas posteriores del proceso.

