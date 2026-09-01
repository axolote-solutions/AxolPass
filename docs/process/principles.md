# Principles

## Objetivo
Definir los principios fundamentales que guían el proceso de desarrollo del proyecto AxolPass.  
Estos principios establecen **cómo pensamos**, **cómo trabajamos** y **cómo tomamos decisiones**, independientemente de la arquitectura, tecnología o requerimientos funcionales que se definan posteriormente.

Los principios aquí establecidos son **obligatorios** para todos los colaboradores del proyecto.

---

## Alcance
Este documento aplica a:

- decisiones del proceso  
- decisiones de documentación  
- decisiones de colaboración  
- decisiones de calidad  
- decisiones de gobernanza del monorepo  

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
La prioridad del proyecto es la claridad:  
- claridad en documentación  
- claridad en decisiones  
- claridad en estructura  
- claridad en comunicación  

La velocidad nunca debe comprometer la claridad.

---

## 2. **Documentar antes de construir**
Cada etapa del proyecto debe estar documentada antes de iniciar cualquier implementación.  
La documentación es parte del producto, no un accesorio.

---

## 3. **Decisiones explícitas, nunca implícitas**
Toda decisión relevante debe quedar registrada en:

- un documento del proceso  
- un ADR  
- un Issue  
- un Pull Request  

No se permiten decisiones tácitas, verbales o asumidas.

---

## 4. **Proceso primero, tecnología después**
El proceso define cómo trabajamos.  
La tecnología se elige cuando el proceso está claro.  
Nunca se selecciona tecnología sin contexto, requerimientos o análisis.

---

## 5. **Monorepo como fuente de verdad**
El monorepo AxolPass es:

- el repositorio único  
- la fuente de verdad  
- el lugar donde vive todo el proyecto  

No se permiten repos paralelos sin justificación formal.

---

## 6. **Ramas limpias, PRs pequeños**
Las ramas deben ser:

- específicas  
- cortas  
- descriptivas  
- alineadas con naming-conventions  

Los Pull Requests deben ser:

- pequeños  
- revisables  
- trazables  
- vinculados a Issues  

---

## 7. **Trazabilidad total**
Cada documento, cada cambio y cada decisión debe poder rastrearse:

- quién lo hizo  
- cuándo lo hizo  
- por qué lo hizo  
- cómo se aprobó  

La trazabilidad es un principio central del proyecto.

---

## 8. **Neutralidad tecnológica en el Paso‑1**
Durante el Paso‑1:

- no se define arquitectura  
- no se define infraestructura  
- no se define tecnología  
- no se definen microservicios  
- no se definen bases de datos  
- no se define UI  

El proceso debe ser independiente de la solución técnica.

---

## 9. **Consistencia sobre preferencia personal**
Las decisiones deben seguir:

- estándares  
- convenciones  
- principios  
- documentación  

No preferencias individuales.

---

## 10. **Evolución controlada**
El proceso puede evolucionar, pero:

- solo mediante ADRs  
- solo mediante discusión  
- solo mediante consenso  
- nunca de forma accidental  

---

## Consideraciones

- Estos principios son parte del **Paso‑1** y aplican desde el inicio del proyecto.  
- Cualquier excepción debe documentarse mediante un ADR en `/docs/decisions`.  
- Los principios pueden ampliarse conforme el proyecto crezca.  
- Los principios deben ser respetados por todos los colaboradores.

---

## Estado Actual
Este documento completa la definición conceptual del proceso AxolPass en el **Paso‑1**.  
Los siguientes pasos se enfocarán en cerrar la documentación del proceso y preparar la transición hacia el análisis de dominio.

