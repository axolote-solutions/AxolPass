# Documento de Visión y Caso de Negocio: AxolPass

## 1. Justificación de Negocio

El mercado de administración de condominios y fraccionamientos residenciales enfrenta tres problemas críticos que impactan directamente en la plusvalía de las propiedades y la calidad de vida de los residentes:

1. **Fricción y Vulnerabilidad en Accesos:** El registro manual de visitantes en casetas genera cuellos de botella, errores humanos y fugas de seguridad.
2. **Morosidad y Falta de Herramientas de Cobro:** Las administraciones locales carecen de mecanismos automatizados y coercitivos para incentivar el pago de cuotas de mantenimiento.
3. **Gestión Caótica de Amenidades:** El descontrol en los espacios de estacionamiento para visitantes genera conflictos vecinales constantes.

**La Solución (AxolPass):**
AxolPass es una plataforma B2B SaaS (Software as a Service) que digitaliza y automatiza el control de accesos vehiculares y peatonales. Axolote Solutions comercializará el sistema mediante contratos anuales basados en la capacidad instalada (número de casas). La plataforma no solo moderniza el acceso físico mediante hardware dual (QR/PIN) y validación offline, sino que actúa como una herramienta de gestión comunitaria al permitir a los administradores restringir el acceso y la generación de invitaciones a los residentes morosos, incentivando la recaudación del fraccionamiento.

## 2. Listado de Stakeholders (Partes Interesadas)

Para garantizar el éxito del proyecto, debemos alinear los intereses de los siguientes grupos:

**Stakeholders Internos (Axolote Solutions)**

* **Sponsors / Inversionistas:** Proveen el capital inicial para el desarrollo del software y hardware. Su interés es el retorno de inversión (ROI) y la escalabilidad del modelo B2B.
* **SystemAdmin (Operaciones/Soporte):** Encargados de dar de alta nuevos clientes, gestionar la facturación y monitorear la salud del sistema.

**Stakeholders Externos (El Cliente y sus Usuarios)**

* **Mesa Directiva / Comité de Vigilancia:** Los tomadores de decisión que firman el contrato con Axolote Solutions. Buscan seguridad, orden y herramientas para reducir la morosidad.
* **CommunityAdmin (Administrador Local):** El usuario clave que opera el sistema en el día a día. Gestiona altas, bajas, suspensiones y analiza reportes.
* **Residentes (Principales y Secundarios):** Los usuarios finales de la app móvil. Buscan comodidad, rapidez para invitar a sus conocidos y privacidad de sus datos.
* **Guardias de Seguridad:** El personal en caseta. Requieren un sistema infalible, fácil de usar y que no entorpezca su labor bajo presión o fallas de internet.

## 3. Manejo de Riesgos (Risk Management)

Hemos identificado los riesgos críticos del proyecto y sus estrategias de mitigación:

| Riesgo Comercial / Operativo | Impacto | Estrategia de Mitigación |
| --- | --- | --- |
| **Caída de Internet en Caseta** | Alto. Bloqueo de accesos y caos vial. | Desarrollo de un mecanismo de caché local que permita validar invitaciones y operar la barrera en modo *offline*. |
| **Fallas en Hardware de Lectura** | Alto. Frustración del usuario por exposición a la intemperie. | Implementación de credenciales duales (QR y PIN numérico). Inclusión de una consola para que el guardia realice aperturas manuales auditadas. |
| **Morosidad del Fraccionamiento** | Medio. Pérdida de ingresos para Axolote Solutions. | Ejecución de un "Blackout Comercial": el sistema bloquea automáticamente todas las operaciones del cliente si no se registra el pago mensual. |
| **Baja Adopción Tecnológica** | Medio. Residentes mayores que no saben usar la app. | Distribución omnicanal de los códigos de acceso (WhatsApp y Correo), reduciendo la necesidad de que el visitante o el residente dependan exclusivamente de una app nativa. |
| **Privacidad de Datos (Data Breach)** | Crítico. Riesgo legal por exposición de información personal. | Anonimización de datos en los reportes del `CommunityAdmin`. Aislamiento estricto (Multi-tenant) en la base de datos por fraccionamiento. |

## 4. Métricas de Éxito (KPIs)

Para medir objetivamente si AxolPass está cumpliendo sus objetivos de negocio, monitorearemos las siguientes métricas:

**Métricas Comerciales (Axolote Solutions):**

* **MRR (Monthly Recurring Revenue):** Crecimiento de los ingresos recurrentes mensuales por la suma de contratos activos.
* **Customer Churn Rate:** Porcentaje de fraccionamientos que no renuevan su contrato anual o son dados de baja por morosidad extrema.

**Métricas Operativas y de Adopción (Salud del Producto):**

* **Tasa de Adopción por Fraccionamiento:** Porcentaje de casas activas que generan al menos una invitación por semana respecto al total de casas contratadas.
* **Tasa de Automatización en Caseta:** Porcentaje de accesos validados automáticamente (QR/PIN) *versus* los ingresos manuales procesados por el guardia (excepciones o llegadas sorpresa). Un porcentaje alto indica que el sistema funciona fluidamente.
* **Tiempo de Uptime del Sistema:** Disponibilidad del servicio core y latencia en la lectura de los códigos en caseta.

