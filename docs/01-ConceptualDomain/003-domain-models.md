# 🌱 Modelos del Dominio – AxolPass (Etapa 1 – Conceptual)

Este documento define los **conceptos centrales del negocio** de AxolPass.  
Cada modelo representa una idea del dominio, sus atributos conceptuales y sus reglas naturales, sin tecnología ni detalles de implementación.

---

# 1. Área: Accesos y Control Operativo

## Invitación  
Permiso temporal para que un visitante ingrese a la comunidad.

**Atributos conceptuales:**  
- visitante  
- tipo de visita  
- fecha programada  
- estado conceptual (vigente, no vigente)

**Reglas del negocio:**  
- solo puede emitirse si la vivienda está activa  
- debe corresponder a una fecha válida  
- debe respetar las políticas de la comunidad  
- puede ser cancelada por el residente

---

## Pase de Acceso  
Representación del permiso otorgado a un visitante.

**Atributos conceptuales:**  
- identificador del pase  
- vínculo con la invitación  
- medio de presentación (conceptual)

**Reglas del negocio:**  
- debe ser verificable por la comunidad  
- debe corresponder a una invitación válida

---

## Evento de Acceso  
Registro conceptual de una entrada o salida.

**Atributos conceptuales:**  
- fecha y hora  
- tipo de acceso  
- actor involucrado

**Reglas del negocio:**  
- todo acceso debe quedar registrado  
- accesos excepcionales requieren justificación  
- accesos no justificados pueden considerarse incidentes

---

# 2. Área: Gestión de Comunidad

## Comunidad  
Entidad organizativa y física donde se administra el acceso.

**Atributos conceptuales:**  
- nombre  
- parámetros operativos  
- capacidad general

**Reglas del negocio:**  
- define las políticas operativas para viviendas y accesos  
- establece límites y configuraciones generales

---

## Vivienda  
Unidad privativa dentro de la comunidad.

**Atributos conceptuales:**  
- identificación única  
- estado operativo

**Reglas del negocio:**  
- su identificación debe ser única  
- su estado afecta invitaciones y permisos  
- la suspensión no afecta accesos ya realizados

---

## Vínculo de Residencia  
Relación entre una persona y una vivienda.

**Atributos conceptuales:**  
- rol del residente  
- vigencia del vínculo

**Reglas del negocio:**  
- solo puede existir un residente principal  
- la remoción del residente principal afecta a los secundarios

---

# 3. Área: Administración Comercial

## Suscripción  
Relación comercial entre Axolote y la comunidad.

**Atributos conceptuales:**  
- capacidad contratada  
- condiciones comerciales  
- vigencia

**Reglas del negocio:**  
- los términos acordados deben respetarse  
- la vigencia afecta el estado operativo de la comunidad

---

## Perfil Financiero  
Modelo conceptual que representa las reglas comerciales.

**Reglas del negocio:**  
- los pagos afectan la vigencia de la suscripción  
- los cálculos deben ser coherentes con las condiciones comerciales

---

# 4. Área: Identidad y Usuarios

## Usuario  
Representación de una persona dentro del sistema.

**Atributos conceptuales:**  
- estado de la cuenta  
- información de contacto

**Reglas del negocio:**  
- la identidad debe ser única  
- la autenticación puede delegarse, pero la autorización es local

---

## Alcance Operativo  
Límite conceptual de acción de un usuario dentro de una comunidad.

**Reglas del negocio:**  
- un usuario solo puede operar dentro de su comunidad asignada

---

# 5. Área: Notificaciones y Auditoría

## Política de Notificación  
Reglas que gobiernan la comunicación hacia usuarios.

**Atributos conceptuales:**  
- prioridad  
- canal conceptual  
- vigencia del mensaje

**Reglas del negocio:**  
- debe respetar preferencias del usuario  
- mensajes críticos pueden tener prioridad especial

---

## Registro de Auditoría  
Historial conceptual de acciones relevantes.

**Atributos conceptuales:**  
- actor  
- momento  
- acción realizada

**Reglas del negocio:**  
- los registros deben ser permanentes  
- no deben eliminarse acciones críticas

