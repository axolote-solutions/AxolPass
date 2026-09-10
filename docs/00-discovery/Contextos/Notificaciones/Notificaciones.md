# 1. Requerimientos Funcionales (FR) - Mensajería Transaccional y Alertas

## 1.1. Alertas Operativas y de Seguridad (Tiempo Real)**
* **Notificación de Acceso a Residentes:** El sistema debe alertar al residente (principal o secundario) a través de una notificación Push en su dispositivo móvil en el momento exacto en que su visita o proveedor valide su acceso en la caseta.
* **Alerta Crítica (Botón de Pánico):** Al detonarse una emergencia, el sistema despachará una alerta prioritaria (idealmente capaz de evadir modos silenciosos básicos) a las pantallas activas de los guardias (`SECURITY_GUARD`) y a los administradores locales (`CommunityAdmin`).

## 1.2. Mensajería Transaccional (Comunicación Externa)**
* **Distribución de Invitaciones:** El sistema debe proveer la capacidad de enviar los pases de acceso (Códigos QR o enlaces) a los visitantes externos a través de canales estándar (correo electrónico, SMS o WhatsApp, según la integración elegida) en nombre del residente.
* **Aprovisionamiento de Cuentas de Personal:** El módulo será el responsable de enviar los correos electrónicos con los enlaces seguros (Magic Links o setup de contraseña) cuando la administración dé de alta a un nuevo personal (guardia o administrador) operativo (Contexto IAM).
* **Recibos y Alertas de Suscripción:** Envío automatizado de comprobantes de pago o alertas de corte de servicio a los administradores del fraccionamiento (Contexto de Suscripción).

## 1.3. Gestión de Preferencias de Usuario**
* **Control de Silencio (Opt-out parcial):** Los residentes deben tener la capacidad de habilitar o deshabilitar las notificaciones Push de eventos rutinarios (ej. "Llegada de Visitas"), pero el sistema bloqueará la opción de desactivar notificaciones catalogadas como "Seguridad" o "Administrativas Críticas".

---

# 2. Requerimientos No Funcionales (NFR) - Confiabilidad y Entregabilidad

## 2.1. Rendimiento y Tolerancia a Fallos (Reliability)**
* **Procesamiento Asíncrono (Desacoplamiento):** El envío de cualquier notificación (Push, Email, SMS) jamás debe bloquear el hilo principal de ejecución. Debe procesarse en segundo plano (mediante colas de eventos) para garantizar que la operación original (ej. abrir la pluma) responda en milisegundos.
* **Políticas de Reintento Automático:** Si el proveedor externo de mensajería falla o no responde, el sistema encolará el mensaje y aplicará un esquema de reintentos escalonados (exponential backoff) para intentar la entrega nuevamente sin saturar la red.
* **Caducidad de Mensajes Operativos (TTL):** Las alertas dependientes del tiempo (como la llegada de una visita) tendrán un tiempo de vida corto. Si ocurre una caída grave y el mensaje no puede enviarse en un lapso de 10 minutos, el sistema descartará la alerta en vivo para no confundir al residente con notificaciones desfasadas.

## 2.2. Entregabilidad e Infraestructura**
* **Delegación a Proveedores Especializados:** Para evitar bloqueos por SPAM y garantizar la escalabilidad, AxolPass no operará servidores de correo o mensajería propios. Todo despacho será delegado a plataformas en la nube especializadas (ej. Firebase Cloud Messaging para Push, SendGrid/AWS SES para correos electrónicos).
* **Limpieza de Dispositivos (Token Management):** El sistema detectará y purgará automáticamente los identificadores de dispositivos (Device Tokens) inválidos o expirados cuando las plataformas externas reporten que un usuario desinstaló la aplicación.

