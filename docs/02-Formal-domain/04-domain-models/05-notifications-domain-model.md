# Notifications domain model

## 5. Contexto de Notificaciones y Alertas

**Propósito:** Orquestar el contacto hacia el mundo exterior, estandarizando los canales, formatos y niveles de urgencia.

### **Entidades del Dominio:**
* **Política de Notificación:** Las directrices unificadas sobre cómo y a quién despachar un evento.

* **Pase de Acceso:** El material visual y logístico entregado al visitante.

* **Alerta Crítica:** Un aviso transaccional que exige respuesta o atención inmediata.

### **Value Objects Conceptuales:**
* **Canal de Entrega:** El medio tecnológico (Correo, Push, WhatsApp).

* **Preferencia de Notificación:** El nivel de silencio solicitado por un usuario.

### **Invariantes del Negocio:**
* Los pases de acceso jamás se envían mediante enlaces web; toda la información va incrustada en la plantilla.

* Las alertas de seguridad física tienen un periodo de vida (TTL) muy corto; si la red falla, se descartan para no asustar al residente horas después.

* Los avisos administrativos severos (como el apagón comercial o botón de pánico) ignoran las preferencias de silencio del residente.

### **Reglas Internas del Contexto:**
* Las plantillas de mensaje son dinámicas y se adaptan a la naturaleza del hecho (ej. cambia si es una pizza o si es un familiar).

* Si un proveedor externo reporta un fallo permanente en el número de teléfono, el contexto notifica de regreso al dominio para que inactive la invitación.

### **Estados y Transiciones (Ciclo del Mensaje):**
* *Encolado* $\rightarrow$ *Despachado* o *Fallido Permanentemente*.

### **Relaciones entre Entidades:**
* La *Política de Notificación* determina el *Canal de Entrega* y evalúa la *Preferencia de Notificación* del usuario antes de despachar el *Pase de Acceso*.

### **Límites del Agregado (Candidatos):**
* **Agregado Raíz:** *Política de Notificación* (Garantiza el cumplimiento de privacidad y reintentos).

### **Lenguaje Ubicuo del Contexto:**
* NotificationPolicy, CriticalAlert, AccessPass, DeviceToken, DeliveryChannel, NotificationPreference, OfflineOverrideAlert.
