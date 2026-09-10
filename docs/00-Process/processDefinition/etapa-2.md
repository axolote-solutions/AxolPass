# ⭐ Etapa 2 – Análisis de Dominio Profundo (DDD)  
**Objetivo:** transformar el entendimiento conceptual del negocio (Etapa 1) en **modelos de dominio formales**, **bounded contexts formales**, **agregados**, **invariantes**, **reglas explícitas**, **procesos detallados**, y **event storming estructurado**.

Etapa 2 es donde el dominio deja de ser “conceptual” y se vuelve **formal**, **estructurado**, **riguroso**, pero aún **sin arquitectura técnica**.

---

# 🎯 Qué produce la Etapa 2 (solo definición, no ejecución)

A continuación tienes la lista **completa y oficial** de artefactos de Etapa 2.

Cada uno está explicado con claridad para que sepas exactamente qué es y para qué sirve.

---

## 1. **Bounded Contexts Formales (BC v2)**  
Versión formal de los bounded contexts conceptuales de Etapa 1.

Incluye:

- propósito del contexto  
- límites explícitos  
- responsabilidades  
- reglas internas  
- reglas externas  
- lenguaje ubicuo específico del contexto  
- relaciones con otros contextos (conceptuales, no técnicas)  

**No incluye tecnología.**

---

## 2. **Mapa de Contextos Formal (Context Map v2)**  
Versión formal del context map conceptual.

Incluye:

- relaciones entre contextos  
- tensiones del negocio  
- dependencias naturales  
- flujos conceptuales  
- zonas de colaboración  
- zonas de autonomía  

**Sin patrones DDD técnicos todavía (eso es Etapa 3).**

---

## 3. **Event Storming Conceptual (Big Picture)**  
Un recorrido completo del dominio usando eventos del negocio.

Incluye:

- eventos del negocio  
- comandos del negocio  
- actores del negocio  
- políticas del negocio  
- procesos del negocio  
- estados del negocio  

**Sin eventos técnicos, sin colas, sin arquitectura.**

---

## 4. **Modelos de Dominio Formales (Domain Models v2)**  
Versión formal de los modelos conceptuales.

Incluye:

- entidades del dominio  
- value objects conceptuales  
- invariantes del negocio  
- reglas explícitas  
- estados del negocio  
- transiciones del negocio  

**Sin clases, sin código, sin persistencia.**

---

## 5. **Agregados Conceptuales (Aggregate Candidates)**  
Identificación de los agregados del dominio.

Incluye:

- raíz del agregado  
- límites del agregado  
- invariantes que protege  
- reglas que encapsula  
- relaciones con otros agregados  

**Sin diseño técnico de agregados (eso es Etapa 3).**

---

## 6. **Procesos del Negocio Detallados (Business Processes v2)**  
Versión detallada de los procesos conceptuales.

Incluye:

- pasos del negocio  
- decisiones del negocio  
- reglas aplicadas en cada paso  
- eventos detonados  
- actores involucrados  
- estados antes/después  

**Sin flujos técnicos, sin tokens, sin QR, sin IdP.**

---

## 7. **Casos de Uso Conceptuales (Use Cases v1)**  
Los casos de uso del negocio, sin tecnología.

Incluye:

- actor  
- intención  
- precondiciones del negocio  
- postcondiciones del negocio  
- reglas del negocio  
- eventos del negocio  

**Sin UI, sin API, sin pantallas, sin endpoints.**

---

## 8. **Políticas del Negocio (Business Policies)**  
Reglas que se aplican automáticamente cuando ocurre un evento.

Incluye:

- disparador  
- condición  
- acción del negocio  
- restricciones  

**Sin colas, sin cron jobs, sin interceptores.**

---

## 9. **Estados del Negocio (State Models)**  
Modelos de estados conceptuales.

Incluye:

- estados válidos  
- transiciones válidas  
- reglas que gobiernan cada transición  

**Sin máquinas de estados técnicas.**

---

## 10. **Diccionario del Lenguaje Ubicuo por Contexto**  
Versión extendida del lenguaje ubicuo.

Incluye:

- términos  
- definiciones  
- ejemplos  
- anti‑ejemplos  
- contexto donde aplica  

---

# 🧩 Resumen de la Etapa 2

| Artefacto | Propósito |
|----------|-----------|
| **Bounded Contexts Formales** | Definir límites del dominio con precisión |
| **Context Map Formal** | Entender relaciones entre contextos |
| **Event Storming Conceptual** | Descubrir procesos, reglas y eventos |
| **Domain Models Formales** | Estructurar entidades y reglas del negocio |
| **Aggregate Candidates** | Identificar límites de consistencia |
| **Business Processes v2** | Detallar cómo funciona el negocio |
| **Use Cases Conceptuales** | Definir intenciones del negocio |
| **Business Policies** | Reglas automáticas del negocio |
| **State Models** | Estados y transiciones del negocio |
| **Ubiquitous Language por Contexto** | Precisión semántica |
