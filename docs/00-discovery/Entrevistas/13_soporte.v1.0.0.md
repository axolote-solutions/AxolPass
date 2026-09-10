# 🧩 Bloque 13: Soporte y Ayuda

## 🎤 Entrevistas Simuladas (Stakeholders y Analista)

---

### 👩‍💼 CommunityAdmin – Administrador del Fraccionamiento

**Analista:** ¿Qué haces actualmente si tienes un problema con la aplicación?  
**CommunityAdmin:** Normalmente le escribo por WhatsApp a alguien de Axolote, o llamo al contacto que me dieron cuando instalamos el sistema. Pero sería mejor si hubiera una opción directa desde la app, como un botón de “Soporte”.

**Analista:** ¿Qué tipo de problemas enfrentas normalmente?  
**CommunityAdmin:** A veces los residentes no pueden entrar, el QR no funciona, o los guardias tienen dudas. También me han pedido ayuda para configurar usuarios, cambiar datos de casas, etc.

**Analista:** ¿Te gustaría que hubiera un historial de tus solicitudes de soporte?  
**CommunityAdmin:** Sí, sobre todo para saber si ya están atendidas o si tengo que insistir.

---

### 🧍‍♂️ Residente Principal

**Analista:** ¿Has necesitado soporte con la app?  
**Residente:** Sí, un par de veces. Me costó trabajo entender cómo registrar una visita, y otra vez la app no me dejaba entrar.

**Analista:** ¿Qué hiciste?  
**Residente:** Busqué en Google, pero no encontré nada. Terminé preguntándole al vigilante o al administrador. Sería muy útil que dentro de la app hubiera una sección de ayuda, con explicaciones paso a paso o videos cortos.

**Analista:** ¿Te gustaría contactar a soporte desde la app?  
**Residente:** Claro. Un botón que abra WhatsApp o que mande un mensaje directo sería ideal.

---

### 👨‍✈️ Guardia de Seguridad

**Analista:** ¿Tienes acceso a ayuda o soporte cuando algo falla?  
**Guardia:** No mucho. A veces llamo al administrador o le tomo foto a la pantalla y la mando por WhatsApp. Pero no siempre responden rápido.

**Analista:** ¿Te gustaría tener una opción directa en la app de guardia?  
**Guardia:** Sí, aunque lo mejor sería tener una guía rápida dentro de la app, con qué hacer si el lector falla o si un QR no pasa.

---

### 🛠 SystemAdmin – Axolote Solutions

**Analista:** ¿Cómo gestionan actualmente el soporte?  
**SystemAdmin:** Hoy recibimos mensajes por WhatsApp, llamadas o correos, dependiendo del cliente. No está centralizado y a veces se pierde trazabilidad.

**Analista:** ¿Les gustaría integrar un módulo de soporte dentro de la app?  
**SystemAdmin:** Sí, con dos cosas: 1) botón de contacto directo con nosotros (vía mensaje, WhatsApp o correo), y 2) un sistema de tickets interno para registrar problemas, seguimiento y solución.

**Analista:** ¿Y un sistema de ayuda autogestionable?  
**SystemAdmin:** Sería muy útil: una base de conocimiento con preguntas frecuentes, tutoriales y mensajes contextualizados dentro de cada pantalla.

---

## 📝 Notas de Campo del Analista

- Actualmente el soporte es informal: WhatsApp, llamadas y correos.
- Todos los usuarios (residentes, administradores, guardias) piden ayuda contextual dentro de la app.
- Se detecta necesidad de **dos tipos de ayuda**:
  1. **Ayuda autogestionable**: preguntas frecuentes, tutoriales, pasos.
  2. **Contacto con soporte**: vía mensaje directo, WhatsApp o correo.
- CommunityAdmin y SystemAdmin piden historial de tickets, seguimiento y estado de resolución.
- Guardia necesita **protocolos visuales** de qué hacer en caso de errores.
- Se sugiere crear una **base de conocimiento centralizada** visible desde la app, según el tipo de usuario.

---

## 📚 Narrativa Funcional Extendida

El módulo de **Soporte y Ayuda** busca garantizar que todos los usuarios del ecosistema **AxolPass** puedan acceder a asistencia inmediata, confiable y contextual cuando enfrentan problemas o dudas sobre el uso del sistema.

Se identifican **dos tipos de funcionalidades clave**:

### 1. Centro de Ayuda Autogestionable
- Accesible desde todas las versiones de la app (residentes, administradores, guardias).
- Contiene preguntas frecuentes, tutoriales en video o texto, y mensajes contextualizados dentro de cada módulo.
- Adaptado según el perfil del usuario (ej. residentes verán cómo crear invitaciones; guardias verán cómo escanear QR).

### 2. Contacto con Soporte Axolote
- Desde la app, los usuarios pueden:
  - Enviar un mensaje directamente al equipo de soporte.
  - Abrir un chat de WhatsApp preconfigurado.
  - Enviar un correo electrónico desde un formulario.
- Opcionalmente, puede generarse un ticket interno con número de folio, estatus y seguimiento.

Además, los administradores de fraccionamiento podrán consultar su **historial de solicitudes**, ver cuáles han sido atendidas, cuáles están abiertas, y dejar comentarios adicionales si es necesario.

El personal de seguridad contará con una sección específica de **protocolos visuales de emergencia**, para actuar correctamente ante fallas del lector, problemas con QR, o situaciones no contempladas.

Este módulo permite fortalecer la experiencia del usuario, reducir la dependencia de canales informales, y estructurar un servicio de atención más profesional desde **Axolote Solutions**.

