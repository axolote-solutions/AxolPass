# 🌐 Context Map Formal – Etapa 2  
Representa cómo se relacionan conceptualmente los bounded contexts del dominio AxolPass.

---

## 🧩 Relaciones conceptuales entre los bounded contexts

### **1. Suscripción y Facturación ↔ Comunidad**
- Suscripción **habilita** la existencia de una comunidad.  
- Comunidad **consume** el estado comercial para determinar su operatividad.

### **2. Suscripción y Facturación ↔ Accesos**
- El estado comercial **afecta** la capacidad de permitir accesos.  
- Accesos **respeta** la vigencia comercial.

### **3. Suscripción y Facturación ↔ IAM**
- Suscripción **autoriza** la creación de identidades administrativas.  
- IAM **depende** del estado comercial para habilitar o suspender cuentas.

### **4. Suscripción y Facturación ↔ Notificaciones**
- Suscripción **genera eventos** que requieren comunicación (cobranza, suspensión).  
- Notificaciones **distribuye** esos mensajes.

---

### **5. Comunidad ↔ IAM**
- Comunidad **usa** identidades globales para asociar residentes.  
- IAM **define** roles y alcances operativos dentro de la comunidad.

### **6. Comunidad ↔ Accesos**
- Comunidad **provee reglas operativas** (aforo, horarios, estado de viviendas).  
- Accesos **valida** cada entrada contra esas reglas.

### **7. Comunidad ↔ Notificaciones**
- Comunidad **provee destinatarios** (residentes, administradores).  
- Notificaciones **envía** comunicaciones basadas en eventos comunitarios.

---

### **8. IAM ↔ Accesos**
- IAM **define** quién puede operar en el sistema (guardias, admins, residentes).  
- Accesos **consulta** permisos conceptuales para validar acciones.

### **9. IAM ↔ Notificaciones**
- IAM **define** identidades y preferencias conceptuales.  
- Notificaciones **usa** esas identidades para dirigir mensajes.

---

### **10. Accesos ↔ Notificaciones**
- Accesos **genera hechos** (llegadas, salidas, excepciones).  
- Notificaciones **comunica** esos hechos a los actores relevantes.

---

# 📄 PlantUML – Context Map Formal (Etapa 2)

```plantuml
@startuml
title AxolPass – Context Map (Etapa 2 – Formal)

' Configuración general de los rectángulos
skinparam rectangle {
  BorderColor #555555
  RoundCorner 15
  FontColor #111111
  ' Sombra ligera para separarlos del fondo sin distraer
  shadowing true 
}

' Configuración de las líneas y texto de las relaciones 
' (Gris para que no compitan visualmente con los módulos)
skinparam arrow {
  Color #888888
  FontColor #444444
  FontSize 12
}

' Definición de módulos con colores pastel semánticos para máxima legibilidad:
' Verde suave (Facturación/Dinero)
rectangle "Suscripción y Facturación" as Subscription #E8F5E9
' Azul suave (Core/Comunidad/Usuarios)
rectangle "Gestión de Comunidad" as Community #E3F2FD
' Morado suave (Infraestructura de Identidad/Auth)
rectangle "Identidad (IAM)" as IAM #F3E5F5
' Rojo/Rosado tenue (Control, barreras, seguridad)
rectangle "Control de Accesos" as Access #FFEBEE
' Naranja/Durazno tenue (Alertas, mensajes, atención)
rectangle "Notificaciones y Alertas" as Notify #FFF3E0

' === Relaciones conceptuales (sin direcciones, sin patrones, sin tecnología) ===

Subscription -- Community : Relación conceptual\n(Habilita comunidad)
Subscription -- IAM : Relación conceptual\n(Habilita identidades admin)
Subscription -- Access : Relación conceptual\n(Estado comercial afecta accesos)
Subscription -- Notify : Relación conceptual\n(Eventos comerciales requieren comunicación)

Community -- IAM : Relación conceptual\n(Asociación de residentes)
Community -- Access : Relación conceptual\n(Reglas operativas)
Community -- Notify : Relación conceptual\n(Destinatarios de mensajes)

IAM -- Access : Relación conceptual\n(Autorización conceptual)
IAM -- Notify : Relación conceptual\n(Identidades para comunicación)

Access -- Notify : Relación conceptual\n(Hechos operativos generan mensajes)

@enduml
```

![Formal context map](./diagrams/02-Bounded-context-map.png)
