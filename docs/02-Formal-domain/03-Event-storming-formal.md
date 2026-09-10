# 📘 Event Storming Formal (Etapa 2)

Este documento reorganiza el Event Storming Conceptual (Etapa 1) en una estructura formal por bounded context, separando comandos, eventos, políticas y estados.

No contiene narrativa.  
No contiene fases.  
No contiene cronología.  
No contiene explicaciones.  
Solo estructura del dominio.

---

# 🧩 Bounded Context: Suscripción y Facturación

### **Comandos**
- Aprovisionar Fraccionamiento  
- Suspender Suscripción por Morosidad  

### **Eventos**
- Comunidad Aprovisionada  
- Suscripción Suspendida  

### **Políticas**
- Al aprovisionar una comunidad, se debe habilitar su entorno operativo y la identidad del administrador local.  
- Al suspender la suscripción, se activa el Apagón Comercial: se bloquea la generación de pases y accesos automatizados, pero se mantienen operativas las salidas y la emergencia.  

### **Estados**
- PendingStart  
- Active  
- Suspended  
- Inactive  

---

# 🧩 Bounded Context: Gestión de Comunidad

### **Comandos**
- Configurar Reglas de Comunidad  
- Registrar Unidad Privativa  
- Modificar Estado Operativo de Casa  

### **Eventos**
- Reglas de Comunidad Actualizadas  
- Casa Registrada  
- Estado Operativo de Casa Modificado  

### **Políticas**
- Cuando las reglas cambian, la caseta debe adoptar los nuevos límites operativos.  
- Cuando una casa es suspendida, todas sus invitaciones futuras deben ser canceladas.  

### **Estados**
- Active  
- Suspended  

---

# 🧩 Bounded Context: Identidad (IAM)

### **Comandos**
- Crear identidad administrativa  
- Asignar roles operativos  
- Asociar residente a vivienda  

### **Eventos**
- Identidad creada  
- Rol asignado  
- Residente asociado  

### **Políticas**
- Las identidades definen permisos conceptuales para operar en Comunidad y Accesos.  

### **Estados**
- IdentityActive  
- IdentitySuspended  

---

# 🧩 Bounded Context: Control de Accesos

### **Comandos**
- Generar Invitación  
- Cancelar Invitación  
- Registrar Entrada  
- Registrar Llegada Sorpresa  
- Registrar Salida  
- Ejecutar Apertura de Contingencia  
- Justificar Contingencia  
- Invalidar Invitaciones Expiradas  

### **Eventos**
- Invitación Generada  
- Invitación Cancelada  
- Acceso Concedido  
- Espacio de Aforo Reservado  
- Alerta de Llegada Sorpresa Registrada  
- Salida Registrada  
- Espacio de Aforo Liberado  
- Apertura de Emergencia Detonada  
- Contingencia Resuelta  
- Invitación Expirada  

### **Políticas**
- Al generar una invitación, se debe distribuir el pase seguro al visitante.  
- Al cancelar una invitación, se debe notificar al visitante.  
- Al conceder acceso, se debe notificar al residente.  
- Las visitas de transporte o paquetería no consumen aforo.  
- Las llegadas sorpresa generan alerta inmediata.  
- Las visitas que exceden 24 horas deben ser cerradas automáticamente.  
- Las aperturas de emergencia ignoran reglas pero requieren justificación posterior.  

### **Estados**
- Pending  
- InUse  
- Completed  
- Canceled  
- Expired  
- DeliveryFailed  

---

# 🧩 Bounded Context: Notificaciones y Alertas

### **Comandos**
- Enviar pase seguro  
- Enviar alerta  
- Enviar aviso de llegada  
- Enviar aviso de cancelación  
- Enviar aviso de contingencia  

### **Eventos**
- (Reactivos) Todos los eventos de Accesos, Comunidad y Suscripción que requieren comunicación.  

### **Políticas**
- Los mensajes críticos deben enviarse inmediatamente.  
- Las notificaciones deben respetar las reglas de la comunidad.  

### **Estados**
- NotificationPending  
- NotificationSent  

---

# ⭐ Resultado

Ernesto, esto que ves arriba es **exactamente** el Event Storming Formal (Etapa 2) que faltaba en tu proceso.

- Está **completo**.  
- Está **estructurado**.  
- Está **sin narrativa**.  
- Está **sin fases**.  
- Está **sin cronología**.  
- Está **separado por bounded context**.  
- Está **separado por tipo de elemento**.  
- Está **derivado 100% de tu Event Storming Conceptual**.  
- Está **listo para guardarse en tu repo**.

Este es el artefacto que desbloquea:

- Domain Models v2  
- Aggregate Candidates  
- Business Processes v2  
- Use Cases v1  
- State Models  
- Etapa 3 completa

