# Mapa de actores

## 1. Actores Internos (Axolote Solutions)

Representan a los dueños, operadores y mantenedores de la plataforma SaaS.

* **Sponsors / Inversionistas:** Son quienes proveen el capital inicial para el desarrollo del software y hardware. Su principal interés radica en la escalabilidad del modelo B2B y en garantizar un retorno de inversión (ROI).


* **SystemAdmin (Operaciones / Soporte):** Es el personal interno de Axolote Solutions con el nivel máximo de permisos globales. Tienen la responsabilidad exclusiva de dar de alta nuevos fraccionamientos (sin permitir el autoregistro público), gestionar la facturación central y monitorear la salud global del sistema.



## 2. Actores Externos (Clientes y Ecosistema del Fraccionamiento)

Representan a quienes firman el contrato, administran el recinto y operan la herramienta en su día a día. A nivel de base de datos, los roles de sistema operan bajo un modelo estricto de Control de Acceso (RBAC) con un *CommunityScope* que limita su visión a su propia comunidad.

* **Mesa Directiva / Comité de Vigilancia:** Son los tomadores de decisión que fungen como representantes legales para firmar el contrato. Aunque no interactúan forzosamente con el software, buscan herramientas que garanticen el orden y reduzcan la morosidad.

* **CommunityAdmin (Administrador Local):** Es el actor que opera el fraccionamiento en el sistema. Gestiona la configuración, da de alta propiedades, y tiene la autoridad para suspender operativamente casas específicas por morosidad interna. El sistema soporta que un mismo usuario administre múltiples fraccionamientos.

* **ResidentMain / PrimaryResident (Residente Principal):** Es el responsable legal u operativo de una unidad privativa (dueño o inquilino). Además de generar invitaciones desde su App, tiene la autoridad para delegar accesos vinculando residentes secundarios a su propiedad.

* **ResidentSecondary / SecondaryResident (Residente Secundario):** Son familiares o compañeros de casa vinculados a una propiedad existente. Pueden utilizar la App para generar y cancelar sus propias invitaciones, pero carecen de permisos para administrar la configuración de la casa.

* **SecurityGuard (Guardia de Seguridad):** Es el personal físico en la caseta responsable de operar la consola (`GuardConsole`). Requieren un sistema infalible bajo presión para registrar visitas sorpresa (sin código), auditar aperturas de emergencia y recibir alertas.

* **Guest (Visitante):** La persona externa (familiar, transporte, paquetería) que requiere ingresar al fraccionamiento. Interactúan indirectamente con el sistema al recibir su `AccessToken` (QR/PIN) vía WhatsApp o Correo y presentarlo en caseta.



## 3. Actores del Sistema (Autómatas y Hardware)

En una arquitectura orientada a eventos, muchos procesos son ejecutados por agentes no humanos.

* **Sistema / Background Workers:** El núcleo asíncrono que reacciona a eventos del dominio (ej. despachar notificaciones push a un residente cuando su visita ingresa) o a rutinas temporales (*Cron Jobs*), como invalidar invitaciones expiradas durante la madrugada o aplicar suspensiones por morosidad.

* **KioskApp (Hardware / Tótem):** La aplicación instalada en las tablets de los carriles vehiculares, orientada al visitante. Actúa de manera autónoma validando códigos (incluso leyendo la firma criptográfica en modo offline) y enviando la señal eléctrica para levantar la barrera física.



## 4. Proveedores Terceros (Delegación de Confianza)

* **Identity Provider (IdP):** Servicios externos (ej. Firebase, Supabase) en quienes AxolPass delega la autenticación, el envío de códigos OTP y la gestión de contraseñas.

* **Pasarela de Pagos Externa:** Proveedores con certificación PCI-DSS (ej. Stripe) encargados de almacenar la bóveda de tarjetas, procesar los cobros transaccionales y notificar a AxolPass mediante Webhooks.

* **Plataformas de Mensajería Push (FCM / APNs):** La infraestructura nativa de Android y Apple encargada de realizar la entrega final de las notificaciones al dispositivo del residente.
