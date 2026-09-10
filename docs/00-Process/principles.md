# Principles (Actualizado con Filosofías Rectoras)

## Objetivo
Definir los principios fundamentales que guían el proceso de desarrollo del proyecto AxolPass.  
Estos principios establecen **cómo pensamos**, **cómo trabajamos** y **cómo tomamos decisiones**, independientemente de la arquitectura, tecnología o requerimientos funcionales que se definan posteriormente.

Las **filosofías rectoras** se incluyen aquí como **intenciones del proceso**, no como decisiones técnicas.

---

## Alcance
Este documento aplica a:

- decisiones del proceso  
- decisiones de documentación  
- decisiones de colaboración  
- decisiones de calidad  
- decisiones de gobernanza del monorepo  
- filosofías que guían el diseño futuro  

No aplica aún a:

- diseño técnico  
- arquitectura  
- infraestructura  
- microservicios  
- bases de datos  
- UI  
- frameworks  

Esos elementos se definirán en etapas posteriores.

---

# Principios del Proceso AxolPass

## 1. **Claridad sobre velocidad**
La claridad es la prioridad del proyecto:  
- claridad en documentación  
- claridad en decisiones  
- claridad en estructura  
- claridad en comunicación  

La velocidad nunca debe comprometer la claridad.

---

## 2. **Documentar antes de construir**
Cada etapa debe estar documentada antes de iniciar cualquier implementación.  
La documentación es parte del producto.

---

## 3. **Decisiones explícitas, nunca implícitas**
Toda decisión relevante debe quedar registrada en:  
- un documento del proceso  
- un ADR  
- un Issue  
- un Pull Request  

No se permiten decisiones tácitas o asumidas.

---

## 4. **Proceso primero, tecnología después**
El proceso define cómo trabajamos.  
La tecnología se elige cuando el proceso está claro.  
Nunca se selecciona tecnología sin contexto.

---

## 5. **Monorepo como fuente de verdad**
El monorepo AxolPass es el repositorio único del proyecto.  
Toda documentación y código vive ahí.

---

## 6. **Ramas limpias, PRs pequeños**
Las ramas deben ser específicas y descriptivas.  
Los PRs deben ser pequeños, revisables y trazables.

---

## 7. **Trazabilidad total**
Cada documento, cambio y decisión debe poder rastrearse:  
- quién lo hizo  
- cuándo lo hizo  
- por qué lo hizo  
- cómo se aprobó  

---

## 8. **Neutralidad tecnológica en el Paso‑1**
Durante el Paso‑1:  
- no se define arquitectura  
- no se define infraestructura  
- no se define tecnología  
- no se definen microservicios  
- no se define UI  

El proceso debe ser independiente de la solución técnica.

---

## 9. **Consistencia sobre preferencia personal**
Las decisiones deben seguir estándares y convenciones, no preferencias individuales.

---

## 10. **Evolución controlada**
El proceso puede evolucionar, pero solo mediante ADRs, discusión y consenso.

---

# Filosofías Rectoras del Proyecto AxolPass  
*(Intenciones del proceso, no decisiones técnicas)*

Estas filosofías guían el pensamiento del proyecto y orientan el diseño futuro, pero **no definen arquitectura ni implementación en el Paso‑1**.

---

## 11. **API‑First**
El proyecto se diseñará pensando primero en las interfaces públicas.  
Esto garantiza claridad, consistencia y comunicación entre componentes.  
No define tecnología ni estilo de API en esta etapa.

---

## 12. **Domain‑Driven Design (DDD) como filosofía, no arquitectura**
DDD se adopta como **forma de pensar el dominio**, no como estructura técnica.  
En el Paso‑1 solo implica:  
- claridad del lenguaje  
- modelos del dominio  
- separación conceptual  
- intención de bounded contexts  

La arquitectura DDD se definirá más adelante.

---

## 13. **Hexagonal como principio, no diseño**
La arquitectura hexagonal se adopta como **filosofía de separación de responsabilidades**, no como estructura de carpetas ni código.  
En el Paso‑1 solo implica:  
- pensar en puertos y adaptadores  
- evitar acoplamiento  
- favorecer testabilidad  

La arquitectura formal se definirá en el Paso‑3.

---

## 14. **Event‑Driven como visión, no implementación**
El proyecto considera la comunicación basada en eventos como una **posible dirección futura**, pero no se define ningún bus, broker o tecnología en esta etapa.  
En el Paso‑1 solo implica:  
- pensar en hechos del dominio  
- pensar en cambios de estado  
- pensar en comunicación desacoplada  

---

## 15. **Security‑First**
La seguridad es un principio rector del proyecto.  
En el Paso‑1 solo implica:  
- pensar en identidad  
- pensar en autorización  
- pensar en trazabilidad  
- pensar en gobernanza  

Las decisiones técnicas de seguridad se definirán más adelante.

---

## Consideraciones
- Estas filosofías **no son decisiones técnicas**.  
- Su propósito es orientar el pensamiento del proyecto desde el inicio.  
- Las decisiones técnicas se documentarán mediante ADRs en `/docs/decisions`.  
- Los principios pueden ampliarse conforme el proyecto crezca.

---

## Estado Actual
Este documento completa la definición conceptual del proceso AxolPass en el **Paso‑1**, incluyendo ahora las filosofías rectoras del proyecto.

