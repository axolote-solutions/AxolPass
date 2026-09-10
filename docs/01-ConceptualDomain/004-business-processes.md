# Procesos del Negocio – AxolPass (Etapa 1 – Conceptual)

Este documento describe los **procesos naturales del negocio** de AxolPass.  
Cada proceso explica cómo interactúan los actores, conceptos y reglas del dominio **sin tecnología ni detalles de implementación**.

---

## 1. Onboarding Comercial de una Comunidad

Proceso mediante el cual una comunidad inicia su relación con Axolote y queda lista para operar.

### Actores
- SystemAdmin  
- CommunityAdmin  

### Flujo del negocio
1. Axolote registra una nueva comunidad y define sus condiciones comerciales.  
2. Se designa un administrador local para operar la comunidad.  
3. Se crea la estructura inicial de la comunidad.  
4. El administrador local establece reglas operativas básicas.  
5. Se registran las viviendas que formarán parte de la comunidad.  

### Eventos relacionados
- Fraccionamiento Registrado  
- Reglas de Comunidad Actualizadas  
- Vivienda Incorporada  
- Residente Principal Asignado  

---

## 2. Ciclo Comercial y Estado Operativo

Proceso que describe cómo la situación comercial de una comunidad afecta su operación.

### Actores
- SystemAdmin  
- CommunityAdmin  

### Flujo del negocio
1. La comunidad mantiene una vigencia comercial que determina su estado operativo.  
2. Cuando la vigencia expira, la comunidad entra en un estado no operativo.  
3. En estado no operativo, ciertas acciones del negocio se restringen.  
4. La comunidad puede recuperar su estado operativo al regularizar su situación comercial.  

### Eventos relacionados
- Estado Comercial Actualizado  
- Operación Comercial Restaurada  

---

## 3. Autorización de Visitas

Proceso mediante el cual un residente permite la entrada de un visitante.

### Actores
- ResidentMain  
- ResidentSecondary  
- Visitante  

### Flujo del negocio
1. Un residente solicita autorizar una visita.  
2. La comunidad valida que la vivienda esté activa y cumpla sus reglas.  
3. Se genera un permiso de acceso para el visitante.  
4. Se comunica al visitante la autorización correspondiente.  

### Eventos relacionados
- Visita Programada  

---

## 4. Acceso a la Comunidad

Proceso que describe la entrada y salida de personas.

### Actores
- Visitante  
- Guardia  
- ResidentMain  

### Flujo del negocio
1. Un visitante se presenta para ingresar.  
2. La comunidad valida que el permiso sea válido.  
3. Se registra el acceso.  
4. El residente es informado de la llegada.  
5. Cuando el visitante se retira, se registra la salida.  

### Eventos relacionados
- Acceso Confirmado  
- Salida Registrada  

---

## 5. Operación en Condiciones Especiales

Proceso que describe cómo la comunidad opera cuando existen condiciones extraordinarias.

### Actores
- Guardia  
- CommunityAdmin  

### Flujo del negocio
1. La comunidad puede operar con restricciones cuando su estado operativo cambia.  
2. En condiciones especiales, ciertos permisos pueden ser limitados.  
3. Acciones críticas como salidas o emergencias deben mantenerse operativas.  
4. La comunidad puede registrar accesos excepcionales cuando sea necesario.  

### Eventos relacionados
- Acceso Excepcional Autorizado  
- Acceso por Emergencia  

---

## 6. Gestión Operativa de Viviendas

Proceso que describe cómo la comunidad administra el estado de sus viviendas.

### Actores
- CommunityAdmin  
- ResidentMain  
- ResidentSecondary  

### Flujo del negocio
1. La comunidad puede modificar el estado operativo de una vivienda.  
2. Cuando una vivienda cambia de estado, sus permisos se ajustan.  
3. Las visitas ya presentes no se ven afectadas por cambios posteriores.  
4. Los residentes asociados ajustan sus permisos según el nuevo estado.  

### Eventos relacionados
- Residente Principal Asignado  
- Vivienda Incorporada  
- Estado Comercial Actualizado  

---

## 7. Auditoría y Excepciones

Proceso que describe cómo la comunidad maneja incidentes y accesos a información sensible.

### Actores
- CommunityAdmin  
- Guardia  

### Flujo del negocio
1. La comunidad registra acciones relevantes para fines operativos y de seguridad.  
2. Los accesos excepcionales quedan registrados para revisión posterior.  
3. En situaciones justificadas, la comunidad puede acceder a información sensible.  
4. Toda acción de auditoría debe quedar documentada.  

### Eventos relacionados
- Acceso a Información Sensible Autorizado  
- Acceso Excepcional Autorizado  
