# **Etapa 3 — Domain Architecture (Arquitectura del Dominio)**

La Etapa 3 define la **arquitectura conceptual del sistema**, es decir, cómo los bounded contexts del dominio **se relacionan**, **se integran**, **se sincronizan** y **se gobiernan** para formar un sistema coherente.  
Esta etapa no es técnica: no describe APIs, bases de datos, microservicios ni infraestructura.  
Su propósito es establecer la **arquitectura del negocio**, previa a cualquier decisión técnica.

Etapa 3 toma los artefactos de Etapa 2 (bounded contexts, domain models, procesos, casos de uso, políticas y estados) y los organiza en una estructura arquitectónica que describe:

- los límites entre contextos,  
- las dependencias conceptuales,  
- los patrones de integración,  
- las políticas transversales,  
- los modelos de estado del negocio,  
- y los servicios conceptuales que coordinan el comportamiento del sistema.

El resultado es una visión clara de **cómo funciona AxolPass como sistema**, antes de traducirlo a microservicios o tecnología.

---

## **Componentes de la Etapa 3 (alto nivel)**

1. **Arquitectura de Bounded Contexts**  
   Relaciones, dependencias conceptuales y patrones de integración entre contextos.

2. **Business Policies**  
   Reglas automáticas del negocio que se ejecutan ante eventos del dominio.

3. **State Models**  
   Estados válidos y transiciones conceptuales de las entidades del dominio.

4. **Domain Integration Architecture**  
   Puertos conceptuales, servicios de dominio y mecanismos de coordinación entre contextos.

---

# **Estructura de Carpetas Oficial de la Etapa 3**

```
/docs/03-domain-architecture/
    01-bounded-contexts-architecture.md
    02-business-policies.md
    03-state-models.md
    04-domain-integration-architecture.md
```

