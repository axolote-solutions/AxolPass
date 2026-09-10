# Paso -1 — Fundamentos del Proyecto  
**Propósito:** Definir la estructura organizativa, nomenclaturas, estándares y parámetros operativos del proyecto **antes** de iniciar análisis de negocio o decisiones arquitectónicas.

---

## 📁 Estructura de Repositorios  
Decisión estratégica: **Monorepo** o **Multirepo**.

### Monorepo (opción recomendada para equipos pequeños/medianos)
```
/architecture
/docs
/common-libraries
/services
    /resident-service
    /invitation-service
    /access-service
    /audit-service
    /admin-service
/infrastructure
    /helm
    /kustomize
    /terraform
```

### Multirepo (opción recomendada para equipos grandes)
Repositorios independientes:
- resident-service  
- invitation-service  
- access-service  
- audit-service  
- admin-service  
- infra-eks  
- shared-libraries  

**Artefacto:**  
`/docs/process/repository-strategy.md`

---

## 🏷️ Nomenclaturas Estándar  
Convenciones globales para mantener coherencia.

### Microservicios  
```
{context}-{service}
```
Ejemplos:
- resident-service  
- invitation-service  
- access-service  

### APIs REST  
```
/api/v1/residents
/api/v1/invitations
/api/v1/access
```

### Eventos (si aplica, sin asumir arquitectura)  
```
resident.created
invitation.created
access.authorized
access.denied
```

### Bases de datos  
```
{service}_db
```

**Artefacto:**  
`/docs/process/naming-conventions.md`

---

## 📂 Estructura de Carpetas por Microservicio (Neutral, sin arquitectura definida)
Esta estructura funciona con cualquier estilo arquitectónico (MVC, Clean, Hexagonal, Onion, CQRS, etc.).

```
/src
  /main
    /java/com/company/{service}
      /api        ← controladores, handlers, endpoints
      /core       ← lógica de negocio o dominio (si aplica)
      /infra      ← infraestructura, integraciones externas
    /resources
      application.yml
  /test
/docs
Dockerfile
README.md
```

**Artefacto:**  
`/docs/process/folder-structure.md`

---

## 📊 SLA, SLO, SLI  
Los parámetros operativos del sistema se definen **antes** del análisis de dominio y **antes** de elegir arquitectura.

### SLA (acuerdos externos)
- Disponibilidad del sistema: **99.9%**  
- Tiempo máximo de respuesta en casetas: **< 300 ms**  
- Registro de acceso completado en: **< 1 s**

### SLO (objetivos internos)
- Latencia p95 por microservicio: **< 150 ms**  
- Procesamiento de eventos: **< 500 ms**  
- Sincronización con casetas: **< 2 s**

### SLI (indicadores medibles)
- Latencia p95/p99  
- Error rate  
- Throughput  
- Uptime por servicio  
- Tiempo de recuperación ante fallos (MTTR)

**Artefacto:**  
`/docs/process/sla-slo-sli.md`

---

## 🧰 Herramientas Base del Proyecto  
Definidas sin comprometer arquitectura.

### Desarrollo
- Java 17  
- Spring Boot 3  
- Maven o Gradle (decisión aquí)  
- Lombok  
- MapStruct  

### Infraestructura
- Docker  
- Docker Compose  
- Kubernetes (EKS)  
- Helm o Kustomize  
- Terraform (si aplica)

### Documentación
- Markdown  
- OpenAPI (para API‑First en etapas posteriores)  
- Miro/FigJam  

### Calidad
- SonarQube  
- Trivy  
- Checkov  

### Seguridad
- Keycloak o AWS Cognito  
- AWS Secrets Manager  

**Artefacto:**  
`/docs/process/tooling.md`

---

## 📄 Artefactos Generados en el Paso -1  
- `repository-strategy.md`  
- `naming-conventions.md`  
- `folder-structure.md`  
- `sla-slo-sli.md`  
- `tooling.md`  
- `principles.md` (si decides documentar filosofía aquí o en Etapa 0)

---

## 🎯 Resultado del Paso -1  
Al finalizar este paso:

- El proyecto tiene **orden estructural**.  
- Los equipos saben **cómo nombrar**, **cómo organizar**, **cómo documentar**.  
- Los SLA/SLO/SLI están claros para futuras decisiones arquitectónicas.  
- El proyecto está listo para iniciar la **Etapa 0 — Aterrizar el problema y el contexto**.

---

Si quieres, puedo generar cada archivo Markdown por separado, por ejemplo:

- **repository-strategy.md**  
- **naming-conventions.md**  
- **folder-structure.md**  
- **sla-slo-sli.md**  
- **tooling.md**  

¿Quieres que generemos los archivos uno por uno?