# **Etapa 4 — Technical Architecture (Definición Ejecutiva)**

La **Etapa 4** establece la **arquitectura técnica completa del sistema**, traduciendo la arquitectura del dominio (Etapa 3) en decisiones técnicas formales, documentadas y trazables.

> Es la etapa donde el sistema deja de ser conceptual y se convierte en una arquitectura técnica real, documentada con **arc42**, **ADR**, y **casos de uso listos para implementación**.

---

## **Qué incluye la Etapa 4 (alto nivel)**

### **1. arc42 — Documentación Arquitectónica Formal**
Arc42 es el **marco oficial** para documentar la arquitectura técnica del sistema.  
Aquí se documenta:

- contexto del sistema  
- estrategia de solución  
- building blocks  
- runtime view  
- deployment view  
- crosscutting concepts  
- quality requirements  
- riesgos  
- decisiones arquitectónicas  

Arc42 es **la columna vertebral** de la Etapa 4.

---

### **2. ADR — Architecture Decision Records**
Los **ADR** documentan cada decisión arquitectónica relevante, incluyendo:

- qué se decidió  
- por qué se decidió  
- alternativas consideradas  
- consecuencias  
- fecha y responsables  

Los ADR garantizan **trazabilidad**, **gobernanza**, y **razón histórica** de la arquitectura.

---

### **3. Casos de Uso para Implementación**
Aquí se definen los **casos de uso técnicos**, derivados de los casos de uso del dominio (Etapa 2), pero ahora expresados en términos de:

- puertos  
- adaptadores  
- flujos técnicos  
- contratos de entrada/salida  
- eventos técnicos  
- reglas de validación  
- errores y excepciones  
- precondiciones y postcondiciones  

> Son los casos de uso que los desarrolladores implementarán directamente en la Etapa 8.

No son historias de usuario.  
No son requisitos funcionales.  
Son **contratos técnicos de comportamiento**.

---

# **Estructura de Carpetas Oficial de la Etapa 4**

```
/docs/04-technical-architecture/
    arc42/
        01-context-and-scope.md
        02-solution-strategy.md
        03-building-block-view.md
        04-runtime-view.md
        05-deployment-view.md
        06-crosscutting-concepts.md
        07-quality-requirements.md
        08-architecture-decisions.md
        09-risks-and-technical-debt.md

    adr/
        adr-0001-title.md
        adr-0002-title.md
        adr-0003-title.md
        ...

    use-cases/
        01-use-case-name.md
        02-use-case-name.md
        03-use-case-name.md
```

---

# **Definición Ejecutiva de los Casos de Uso para Implementación**

Un **caso de uso técnico** es un documento que define:

- **Propósito técnico** del caso de uso  
- **Actor técnico** (servicio, API, evento, UI)  
- **Precondiciones**  
- **Postcondiciones**  
- **Flujo principal técnico**  
- **Flujos alternos**  
- **Contratos de entrada** (DTO, payload, comando)  
- **Contratos de salida** (DTO, evento, respuesta)  
- **Errores y excepciones**  
- **Reglas de validación**  
- **Eventos técnicos emitidos**  
- **Dependencias técnicas** (puertos, adaptadores, servicios)  

> Es el documento que un desarrollador usa para implementar un caso de uso sin ambigüedad.
