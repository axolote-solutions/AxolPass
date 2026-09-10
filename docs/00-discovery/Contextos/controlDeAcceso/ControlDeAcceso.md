# 1. Requerimientos Funcionales (FR) - El Flujo Nominal de Negocio

## 1.1. Generación y Gestión de la Invitación**

* **Captura de datos mínimos:** El sistema debe requerir del visitante: Nombre completo, placas, marca/modelo del vehículo, teléfono y número de acompañantes.
* **Generación Dual de Credenciales:** El sistema generará un "Token de Acceso" único, representable como un código gráfico (QR) y un PIN numérico.
* **Distribución omnicanal:** Se enviarán ambas opciones (QR y PIN) al visitante por WhatsApp y Correo Electrónico.
* **Invitaciones Recurrentes:** El sistema debe permitir la creación de invitaciones recurrentes para visitantes frecuentes, aunque se considere una funcionalidad de baja prioridad para la primera versión.
* **Reenvío y Cancelación:** El residente debe poder reenviar una invitación activa o cancelarla, lo cual invalidará inmediatamente el token en la aplicación y notificará al invitado.

## 1.2. Reglas de Validación, Vigencia y Cuotas (NUEVO)**

* **Validación de Cuota por Casa:** Antes de permitir la generación de una invitación, el sistema debe consultar el límite de invitaciones permitido para esa casa (determinado por el fraccionamiento); si se supera, se impedirá la generación.
* **Vigencia Diaria y Hora Estimada:** La invitación requiere una hora estimada obligatoria, pero será válida durante todo el día programado; el sistema almacenará la hora exacta de acceso para compararla estadísticamente contra la hora estimada.
* **Cruce de Medianoche (Edge Case):** Si un visitante ingresa un día (ej. viernes a las 20:00 hrs) y sale al día siguiente (ej. sábado a las 02:00 hrs), el código por el hecho de haber sido usado en la entrada permanecerá activo exclusivamente para registrar su salida.
* **Expiración automática:** Al finalizar el día programado, si la invitación no tuvo registro de entrada, se invalidará y registrará como "expirada sin uso".

## 1.3. Políticas de Aforo y Operación Automática**

* **Límite Configurable y Descuento:** El fraccionamiento define un límite de espacios para invitados; cada entrada resta un lugar y cada salida lo libera.
* **Rechazo por Capacidad:** Si hay más invitaciones que espacios físicos, el sistema permitirá el acceso solo a los primeros que lleguen.
* **Apertura y Registro Automatizado:** Al escanear un QR válido o teclear un PIN válido, la barrera se abrirá automáticamente sin intervención del guardia. Se registrará la hora exacta y se notificará al residente.
* **Invalidación por Salida:** Físicamente en la puerta o carril de salida, el visitante debe escanear el mismo código para invalidarlo y evitar el reuso.

---

# 2. Protocolos de Contingencia y Fallos Operativos (NUEVO)

## 2.1. Contingencias del Visitante y Seguridad**

* **Llegadas Sorpresa (Sin Invitación):** Si un visitante llega sin invitación previa, el sistema debe proveer una interfaz ("Guard Console") para que el guardia aplique la validación tradicional (llamar a la casa y registrar los datos manualmente), sustituyendo el uso de libretas.
* **Múltiples Intentos Fallidos (Seguridad):** Si el sistema detecta múltiples intentos fallidos con un mismo código o accesos sospechosos, debe bloquear temporalmente las acciones y enviar alertas de seguridad tanto a los guardias como a los residentes afectados.
* **Códigos Duplicados / Reenvío de Capturas:** Como el control inicial es de un solo uso (Anti-passback), si se escanea un código que ya registró una entrada sin su correspondiente salida, la barrera no se activará y se mostrará un mensaje de rechazo en pantalla enviando alerta al guardia.

## 2.2. Contingencias de Hardware y Red**

* **Fallo de Comunicación Post-Lectura:** Si el código se escanea correctamente pero ocurre un error de red antes de abrir el acceso, el sistema debe permitir un reintento y notificar al residente de la anomalía.
* **Operación Offline:** Si el lector y su circuito controlador pierden conexión a internet, utilizarán un caché local para validar el acceso de las invitaciones del día; este caché debe actualizarse periódicamente conectándose al servidor en caseta.
* **Fallo Electromecánico de Barrera:** Si el token es válido pero la barrera no se abre, el guardia recibirá una alerta de error de apertura, procederá manualmente y el sistema registrará este evento de auditoría.

---

# 3. Requerimientos No Funcionales (NFR)

* **Resistencia de Hardware:** El dispositivo lector estará instalado a la intemperie, por lo que su diseño debe prevenir daños ambientales.
* **Intermediación en Caseta:** Existirá un módulo intermedio ("Guard Console" en web o app) que comunicará a los lectores físicos con el backend de AxolPass para mostrar estados de lectura.
* **Auditoría Estricta:** Cualquier tipo de evento (apertura manual, fallas de validación, error de apertura o falta de red) debe generar un log de sistema y notificar a la consola del guardia y/o al administrador del sistema.
