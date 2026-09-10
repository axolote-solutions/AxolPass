# SLA / SLO / SLI

## Objetivo
Definir los parámetros operativos iniciales del sistema AxolPass.  
Este documento establece **expectativas de servicio**, **niveles de desempeño** y **métricas de observabilidad**, sin depender de arquitectura ni implementación técnica.

Los valores aquí definidos son **referenciales** y se ajustarán cuando existan requerimientos formales, diseño técnico y capacidades reales del sistema.

---

## Conceptos Fundamentales

### SLA — *Service Level Agreement*  
Compromiso formal del servicio hacia los usuarios o clientes.  
Define **lo que el sistema promete cumplir**.

### SLO — *Service Level Objective*  
Objetivo interno del equipo para cumplir el SLA.  
Define **lo que el equipo debe alcanzar**.

### SLI — *Service Level Indicator*  
Métrica cuantitativa que permite medir el SLO.  
Define **cómo se mide el desempeño real**.

---

## SLA Iniciales (Referencia)

Los siguientes SLA representan **expectativas generales** del sistema AxolPass.  
No son compromisos legales y se ajustarán cuando existan requerimientos formales.

### Disponibilidad
- **SLA:** 99.9% de tiempo de actividad (uptime) mensual.
- **Justificación:** Estándar competitivo para plataformas B2B SaaS. Aplica exclusivamente a la infraestructura en la nube (API, Base de Datos y Panel Web), sujeto a la cláusula de exclusiones de infraestructura local (caseta).

### Tiempo de Respuesta
- **SLA:** 500 ms para operaciones críticas  
- **SLA:** 1 s para operaciones no críticas

### Tiempo de Recuperación
- **SLA:** 30 minutos para incidentes menores  
- **SLA:** 2 horas para incidentes mayores

### Integridad de Datos
- **SLA:** 100% en operaciones transaccionales  
- **SLA:** 99.9% en operaciones no transaccionales

---

## SLO Iniciales (Referencia)

Los SLO son objetivos internos que permiten cumplir los SLA.

### Disponibilidad
- **SLO:** 99.95% de tiempo de actividad mensual.
- **Meta interna:** Un margen de seguridad más estricto que el SLA (permite ~21 minutos de caída al mes). Exige que la arquitectura implemente alta disponibilidad y cachés (como Redis)[cite: 5] para proteger la consulta de estados operativos.

### Latencia
- **SLO:**  
  - 300 ms para operaciones críticas  
  - 800 ms para operaciones no críticas

### Errores
- **SLO:**  
  - < 0.1% de errores en endpoints críticos  
  - < 1% en endpoints no críticos

### Recuperación
- **SLO:**  
  - 15 minutos para incidentes menores  
  - 1 hora para incidentes mayores

---

## SLI Iniciales (Referencia)

Los SLI son métricas que se medirán cuando exista infraestructura y monitoreo.

### Disponibilidad
- **SLI:** Porcentaje de respuestas HTTP exitosas (códigos 2xx, 3xx y 4xx) frente al total de peticiones en el API Gateway. (Los errores 5xx restan disponibilidad).
- **SLI:** Monitoreo de "Uptime" sintético (pings) desde múltiples regiones hacia los endpoints críticos de la API cada 60 segundos.
- 
### Latencia
- **SLI:** tiempo promedio de respuesta por endpoint.

### Errores
- **SLI:** porcentaje de respuestas con código 5xx y 4xx.

### Recuperación
- **SLI:** tiempo desde la detección del incidente hasta la restauración del servicio.

### Integridad
- **SLI:** porcentaje de operaciones completadas sin pérdida de datos.

---

### Exclusiones del SLA (Punto de Demarcación)

El cálculo del nivel de servicio (SLA) aplica de manera exclusiva a la disponibilidad de la infraestructura en la nube, bases de datos y la API (backend) que se encuentran bajo el control y gestión directa de Axolote Solutions. Las siguientes eventualidades quedan explícitamente excluidas del cálculo de tiempo de inactividad (downtime) y no constituirán una violación a los compromisos de servicio, al considerarse factores de infraestructura externa o responsabilidad de la administración local:

* **Fallas de Conectividad Local (ISP):** Caídas, intermitencias, pérdida de paquetes o cortes de red atribuibles al Proveedor de Servicios de Internet o a la configuración de la red de área local (LAN) en la caseta del fraccionamiento.
* **Fallas de Suministro Eléctrico:** Interrupciones de energía en la caseta que dejen el software inoperante por falta de suministro eléctrico y ausencia de sistemas de respaldo (UPS/Batería). En escenarios de falla total de energía, la operación recae sobre mecanismos ajenos al software (como el botón físico electromecánico directo al motor), lo cual queda fuera del alcance y registro de AxolPass.


* **Averías en Hardware Físico:** Daños mecánicos, desgaste, vandalismo o fallas operativas en las barreras vehiculares, motores, cableado, lectores ópticos, tablets (KioskApp) o computadoras (GuardConsole) que impidan la validación física del acceso.

*Nota operativa:* AxolPass incluye mecanismos de resiliencia tecnológica y modos de operación sin conexión (Modo Offline) para mitigar el impacto de estas caídas locales y proteger el flujo vehicular, tal como se especifica en los "AxolPass requerimientos". Sin embargo, estas capacidades de contingencia no trasladan la responsabilidad del mantenimiento de la infraestructura local (red, energía y hardware) hacia Axolote Solutions ni fungen como garantía vinculante dentro del cálculo del SLA.

---

## Consideraciones

- Estos valores son **referenciales** y se ajustarán cuando existan requerimientos formales.  
- Los SLA/SLO/SLI deben revisarse en cada etapa del proyecto.  
- Las métricas finales se definirán cuando exista arquitectura, infraestructura y monitoreo.  
- Cualquier cambio debe documentarse mediante un ADR en `/docs/decisions`.

---

## Estado Actual
Este documento forma parte del **Paso‑1** y establece únicamente los parámetros operativos iniciales del sistema AxolPass.  
Los valores finales se definirán en etapas posteriores del proceso.

