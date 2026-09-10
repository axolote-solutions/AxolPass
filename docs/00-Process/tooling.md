# Tooling

## Objetivo
Definir las herramientas oficiales utilizadas en el proceso de desarrollo del proyecto AxolPass.  
Este documento establece **qué herramientas son parte del proceso**, **para qué se usan**, y **cómo se integran en el flujo de trabajo**, sin depender de arquitectura, microservicios o decisiones técnicas futuras.

El objetivo es garantizar orden, consistencia y trazabilidad desde el inicio del proyecto.

---

## Alcance
Este documento cubre:

- Herramientas de control de versiones  
- Herramientas de documentación  
- Herramientas de comunicación  
- Herramientas de gestión del proyecto  
- Herramientas de desarrollo y colaboración  
- Herramientas de calidad del proceso  

No cubre:

- Herramientas de infraestructura  
- Herramientas de despliegue  
- Herramientas de monitoreo  
- Herramientas de arquitectura  
- Herramientas específicas de lenguajes o frameworks  

Esas se definirán cuando exista diseño técnico.

---

## Herramientas Oficiales del Proyecto

### 1. GitHub  
**Uso:**  
- Control de versiones  
- Pull Requests  
- Revisión de código  
- Gestión de ramas  
- Issues  
- Wiki (si aplica)  

**Reglas:**  
- Todo el código y documentación vive en el monorepo AxolPass.  
- Toda contribución debe pasar por Pull Request.  
- Las ramas deben seguir las convenciones definidas en **naming-conventions.md**.

---

### 2. Git  
**Uso:**  
- Manejo local del repositorio  
- Creación de ramas  
- Commits  
- Sincronización con GitHub  

**Reglas:**  
- Commits deben ser claros y descriptivos.  
- No se permite trabajar directamente en `main`.  
- `develop` es la rama base de integración.

---

### 3. Markdown  
**Uso:**  
- Documentación del proyecto  
- Bitácoras  
- Estructuras del proceso  
- ADRs  
- Modelos conceptuales  

**Reglas:**  
- Toda documentación debe escribirse en Markdown.  
- Debe almacenarse dentro del directorio `/docs`.  
- Debe seguir las convenciones de nombres definidas en el proceso.

---

### 4. GitHub Issues  
**Uso:**  
- Registro de tareas  
- Registro de bugs  
- Registro de decisiones pendientes  
- Registro de mejoras del proceso  

**Reglas:**  
- Cada documento del proceso debe tener un Issue asociado.  
- Cada Issue debe tener un responsable.  
- Cada Issue debe cerrarse mediante un Pull Request.

---

### 5. GitHub Pull Requests  
**Uso:**  
- Revisión de cambios  
- Discusión de propuestas  
- Validación del proceso  
- Integración de documentación  

**Reglas:**  
- Ningún cambio se integra sin revisión.  
- PRs deben ser pequeños y específicos.  
- PRs deben referenciar Issues relacionados.

---

### 6. GitHub Projects (opcional)  
**Uso:**  
- Organización visual del trabajo  
- Kanban del proceso  
- Seguimiento de etapas  

**Reglas:**  
- Se utilizará solo si el equipo lo considera necesario.  
- No es obligatorio en el Paso‑1.

---

### 7. Editor de Texto / IDE  
**Uso:**  
- Edición de documentación  
- Edición de archivos del repositorio  

**Reglas:**  
- No se define un IDE obligatorio.  
- El editor debe soportar Markdown y Git.  
- No se definen plugins ni extensiones en esta etapa.

---

## Consideraciones

- Este documento define únicamente las herramientas del **proceso**, no de la solución técnica.  
- Las herramientas técnicas (lenguajes, frameworks, bases de datos, infraestructura) se definirán en etapas posteriores.  
- Cualquier cambio en las herramientas del proceso debe documentarse mediante un ADR en `/docs/decisions`.  
- Las herramientas aquí definidas son obligatorias para todos los colaboradores del proyecto.

---

## Estado Actual
Este documento forma parte del **Paso‑1** y establece las herramientas oficiales del proceso AxolPass.  
Las herramientas técnicas se definirán cuando exista arquitectura y requerimientos formales.

