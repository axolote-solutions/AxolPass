# Fase 1: El MVP (Producto Mínimo Viable)

*El núcleo operativo. Esto es lo mínimo que Axolote Solutions necesita para vender el sistema y operar una caseta de manera segura y automatizada.*

## 1. Suscripción y Comunidad (Gestión)**

* Alta manual de fraccionamientos y administradores (`CommunityAdmin`) por parte de Axolote (`SystemAdmin`).
* Registro manual de pagos y ejecución del "Blackout" (suspensión total del fraccionamiento) por falta de pago.
* Alta de casas, asignación de residentes principales y delegación a residentes secundarios.
* Suspensión operativa de casas específicas por morosidad interna.

## 2. Control de Accesos (Caseta)**

* Generación de invitaciones de un solo uso con hora estimada, válidas únicamente durante el día programado.
* Generación dual de Token (QR y PIN numérico) y envío al visitante vía WhatsApp y Correo Electrónico.
* Validación Híbrida de Accesos: Validación primaria en tiempo real (vía internet) conectada al servidor central, con un mecanismo de respaldo automático (Caché Offline) que permite validar las invitaciones del día localmente si la conexión a internet falla, sincronizando los datos al restablecerse la red.
* Políticas de control de aforo vehicular (Estricta, de Excepción y Flexible).
* Consola del Guardia (Guard Console) para registrar llegadas sorpresa, fallos de lectura y aperturas manuales auditadas.

## 3. Notificaciones y Auditoría**

* Notificaciones Push (vía App Móvil) para alertar a los residentes sobre el ingreso o rechazo de sus invitados.
* Registro inmutable de eventos de acceso (timestamp, actor, resultado) visible para los residentes (su casa) y administradores (su fraccionamiento).

---

# Fase 2: Fast Follows (Mejoras Operativas a Corto Plazo)

*Funcionalidades que aportan mucho valor y comodidad, pero que no bloquean la operación básica del día a día. Se desarrollan una vez que el MVP está estable.*

* **Invitaciones Recurrentes:** Capacidad de generar pases para visitantes frecuentes (ej. personal de servicio, niñeras).
* **Rangos de Fechas:** Permitir que una misma persona tenga acceso durante un periodo extendido sin generar invitaciones diarias.
* **Notificaciones por WhatsApp para Residentes:** Expandir las alertas (que en el MVP son solo Push) para que el residente pueda recibir avisos de ingresos a través de WhatsApp.
* **Preferencias de Notificación:** Permitir a los usuarios activar o desactivar ciertos tipos de alertas según su preferencia.
* **Autenticación en Dos Pasos (2FA):** Implementación de seguridad adicional para el inicio de sesión de los administradores y residentes.

---

# Fase 3: Premium y Enterprise (Escalabilidad y Hardware Avanzado)

* Características avanzadas orientadas a fraccionamientos de alto poder adquisitivo o a la automatización comercial de Axolote Solutions. *

* **Reconocimiento de Placas (LPR):** Integración con cámaras especializadas para tomar fotografías de las placas vehiculares y compararlas con la información registrada en la invitación.
* **Marca Blanca / Customización:** Capacidad de personalizar la interfaz de la aplicación móvil para que muestre el logotipo e identidad visual específica de cada fraccionamiento.
* **Integración de Pasarela de Pagos:** Automatización del cobro de suscripciones a los fraccionamientos mediante el cargo automático a tarjetas de crédito.
* **Delegación de Roles:** Creación de subroles y suplentes para la administración local (`CommunityAdmin`).
* **Semáforos Visuales:** Integración de hardware adicional (señales luminosas) en la caseta para agilizar el flujo vehicular indicando accesos permitidos o denegados.
