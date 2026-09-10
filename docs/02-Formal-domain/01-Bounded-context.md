# Bounded Contexts – Etapa 2 (Formales y Conceptuales)

Cada contexto está definido únicamente en términos de **negocio**, **responsabilidad**, **límites**, **reglas**, **lenguaje ubicuo** y **relaciones conceptuales**.  
No contiene decisiones técnicas ni operativas.

---

## 1. Contexto de Suscripción y Facturación  
### Propósito  
Administrar la relación comercial entre Axolote y cada comunidad, definiendo vigencia, capacidad y estado comercial.

### Límites  
- Gestiona únicamente información comercial.  
- No conoce residentes, accesos, dispositivos, reglas operativas ni notificaciones específicas.

### Responsabilidades  
- Registrar comunidades como clientes.  
- Definir capacidad contratada y condiciones comerciales.  
- Actualizar la vigencia comercial.  
- Determinar el estado comercial (vigente / no vigente).

### Reglas internas  
- Los términos comerciales son inmutables una vez establecidos.  
- La vigencia comercial determina el estado operativo comercial.  
- La cancelación preserva el historial comercial.

### Reglas externas  
- El estado comercial afecta la operación de otros contextos (p. ej., acceso, comunidad).

### Lenguaje ubicuo  
**Subscription**, **SubscriptionStatus**, **Capacity**, **CommercialState**, **PaidThrough**, **Plan**, **ActivationStatus**.

### Relaciones conceptuales  
- **Con Comunidad:** habilita la existencia de la comunidad.  
- **Con IAM:** habilita la identidad del administrador local.  
- **Con Accesos:** determina si la comunidad está comercialmente operativa.  
- **Con Notificaciones:** requiere comunicación de cambios comerciales.

---

## 2. Contexto de Gestión de Comunidad  
### Propósito  
Administrar la estructura física, los residentes y las reglas internas de la comunidad.

### Límites  
- No gestiona accesos, permisos de entrada, tokens ni notificaciones.  
- No conoce detalles comerciales más allá del estado operativo general.

### Responsabilidades  
- Registrar viviendas y modelos arquitectónicos.  
- Asociar residentes a viviendas.  
- Definir reglas internas (aforo, horarios, políticas).  
- Gestionar estados operativos de viviendas.

### Reglas internas  
- La nomenclatura de viviendas debe ser única.  
- El aforo debe ser un valor positivo.  
- La remoción del residente principal afecta a los secundarios.  
- La revelación de información sensible requiere justificación.

### Reglas externas  
- La suspensión de una vivienda afecta permisos en otros contextos.

### Lenguaje ubicuo  
**Community**, **House**, **HouseModel**, **Tenancy**, **CommunitySettings**, **OperationalStatus**.

### Relaciones conceptuales  
- **Con Suscripción:** depende de la existencia comercial.  
- **Con IAM:** usa identidades globales para residentes.  
- **Con Accesos:** provee reglas operativas.  
- **Con Notificaciones:** provee destinatarios.

---

## 3. Contexto de Identidad (IAM)  
### Propósito  
Definir quién es cada actor del sistema y qué alcance operativo posee.

### Límites  
- No conoce viviendas, accesos, invitaciones ni estados comerciales.  
- No gestiona autenticación técnica ni credenciales.

### Responsabilidades  
- Registrar identidades de usuarios.  
- Asignar roles y alcances operativos.  
- Revocar o activar identidades.  
- Determinar si un actor tiene permiso conceptual para actuar.

### Reglas internas  
- El alcance operativo de un actor está limitado a su comunidad.  
- Acciones críticas pueden requerir permisos especiales.

### Reglas externas  
- Provee identificadores únicos para evitar duplicación de datos personales.

### Lenguaje ubicuo  
**SystemAdmin**, **CommunityAdmin**, **PrimaryResident**, **SecondaryResident**, **SecurityGuard**, **User**, **Role**, **Scope**.

### Relaciones conceptuales  
- **Con Todos los Contextos:** provee autorización conceptual.

---

## 4. Contexto de Control de Accesos  
### Propósito  
Gestionar el flujo conceptual de entrada y salida de personas y vehículos.

### Límites  
- No conoce detalles comerciales, identidades técnicas, dispositivos ni tokens.  
- No gestiona notificaciones ni reglas internas de comunidad.

### Responsabilidades  
- Validar permisos de acceso.  
- Registrar entradas y salidas.  
- Gestionar excepciones operativas.  
- Aplicar reglas de acceso definidas por Comunidad.

### Reglas internas  
- Accesos excepcionales requieren justificación.  
- La salida de un visitante no afecta aforo si no aplica.  
- Accesos deben registrarse siempre.

### Reglas externas  
- No permite accesos si la vivienda o comunidad están no operativas.

### Lenguaje ubicuo  
**Invitation**, **AccessRecord**, **Visitor**, **AccessDecision**, **AccessException**.

### Relaciones conceptuales  
- **Con Comunidad:** consume reglas operativas.  
- **Con Suscripción:** respeta estado comercial.  
- **Con Notificaciones:** genera hechos que requieren comunicación.

---

## 5. Contexto de Notificaciones  
### Propósito  
Gestionar la comunicación conceptual hacia residentes, visitantes y administradores.

### Límites  
- No evalúa accesos, reglas, estados comerciales ni permisos.  
- No gestiona dispositivos ni canales técnicos.

### Responsabilidades  
- Determinar destinatarios.  
- Formatear mensajes conceptuales.  
- Comunicar eventos relevantes del negocio.  
- Respetar preferencias de comunicación.

### Reglas internas  
- Mensajes críticos pueden ignorar preferencias de silencio.  
- Mensajes deben ser coherentes con el evento que los origina.

### Reglas externas  
- Depende de Comunidad para obtener destinatarios.  
- Depende de Accesos y Suscripción para eventos detonantes.

### Lenguaje ubicuo  
**Notification**, **Message**, **Recipient**, **Alert**, **NotificationPolicy**.

### Relaciones conceptuales  
- **Con Todos los Contextos:** actúa como observador conceptual.  
- **Con Comunidad:** obtiene información de contacto.

