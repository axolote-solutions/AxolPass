# Naming Conventions

## Objetivo
Establecer las reglas de nomenclatura para todos los elementos del proyecto AxolPass.  
Estas convenciones garantizan consistencia, claridad y trazabilidad en el monorepo, independientemente de la arquitectura o tecnología que se defina posteriormente.

Este documento forma parte del **Paso‑1** y se aplica desde el inicio del proyecto.

---

## Alcance
Las convenciones aquí definidas aplican a:

- nombres de servicios  
- nombres de carpetas  
- nombres de APIs  
- nombres de eventos (si los hubiera)  
- nombres de bases de datos  
- nombres de ramas  
- nombres de archivos de documentación  

No aplican aún a:

- clases  
- paquetes  
- entidades  
- value objects  
- componentes de UI  

Esos elementos se definen cuando exista arquitectura y diseño técnico.

---

## Convenciones Generales

### Estilo
- Usar **kebab-case** para nombres de servicios, carpetas y archivos.  
  Ejemplo: `resident-service`, `access-control`, `domain-model.md`

- Usar **snake_case** para nombres de bases de datos.  
  Ejemplo: `resident_service_db`

- Usar **lowercase** para eventos y recursos REST.  
  Ejemplo: `resident.created`, `/api/v1/residents`

- Usar **PascalCase** solo para nombres de documentos conceptuales.  
  Ejemplo: `DomainOverview.md`

---

## Nombres de Servicios

Formato:

```
{context}-{service}
```

Reglas:
- Debe ser descriptivo.  
- Debe representar una responsabilidad clara.  
- No debe incluir tecnología (no usar `-api`, `-spring`, `-react`, etc.).  
- No debe incluir arquitectura (no usar `-hex`, `-clean`, etc.).

Ejemplos futuros (no definidos aún):
- `resident-service`  
- `invitation-service`  
- `access-service`  

---

## Nombres de APIs REST

Formato:

```
/api/v{version}/{resource}
```

Reglas:
- `version` siempre inicia en `v1`.  
- `resource` debe estar en singular.  
- No incluir verbos.  
- No incluir tecnología.

Ejemplos futuros:
- `/api/v1/resident`  
- `/api/v1/invitation`  

---

## Nombres de Eventos (si aplica)

Formato:

```
{context}.{event}
```

Reglas:
- Todo en minúsculas.  
- Separado por punto.  
- Debe representar un hecho del dominio.  
- No incluir tecnología ni arquitectura.

Ejemplos futuros:
- `resident.created`  
- `access.authorized`  

---

## Nombres de Bases de Datos

Formato:

```
{service}_db
```

Reglas:
- Usar snake_case.  
- No incluir tecnología (no usar `_postgres`, `_mongo`, etc.).  
- No incluir ambiente (`_dev`, `_prod`).

Ejemplos futuros:
- `resident_service_db`  
- `access_service_db`  

---

## Nombres de Ramas

Formato general:

```
<tipo>/<nombre>
```

Tipos permitidos:
- `feature/`  
- `fix/`  
- `docs/`  
- `chore/`  

Reglas:
- Usar kebab-case en el nombre.  
- Debe ser corto y descriptivo.  
- Debe derivar de `develop`.

Ejemplos:
- `docs/paso-1`  
- `feature/add-resident-api`  
- `fix/typo-readme`  

---

## Nombres de Archivos de Documentación

Reglas:
- Usar kebab-case.  
- Deben ser descriptivos.  
- Deben reflejar el contenido del documento.  
- No incluir números de versión.

Ejemplos:
- `repository-structure.md`  
- `documentation-structure.md`  
- `naming-conventions.md`  
- `sla-slo-sli.md`  
- `tooling.md`  

---

## Consideraciones

- Estas convenciones aplican desde el Paso‑1 y se mantienen durante todo el proyecto.  
- Si se requiere una excepción, debe documentarse mediante un ADR en `/docs/decisions`.  
- Las convenciones pueden ampliarse cuando se defina arquitectura y diseño técnico.

