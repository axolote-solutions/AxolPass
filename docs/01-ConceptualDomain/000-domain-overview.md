# Domain Overview – AxolPass (Etapa 1 – Conceptual)

Este documento describe el **dominio del negocio** de AxolPass sin tecnología, sin arquitectura y sin patrones técnicos.

Es la visión conceptual que sirve como base para:

* el **lenguaje ubicuo**
* el **domain map**
* los **bounded contexts conceptuales**
* los **modelos del dominio**
* los **procesos del negocio**

---

## 1. Propósito del Dominio

AxolPass es un sistema diseñado para **administrar el acceso de personas y vehículos** a fraccionamientos residenciales y **gestionar la operación de la comunidad**.

El dominio abarca:

* cómo se invita a visitantes.


* cómo se autoriza o deniega un acceso.


* cómo se organiza la comunidad.


* cómo se identifican los usuarios.


* cómo se aplican reglas internas.


* cómo se comunica información relevante.


* cómo se registra la actividad operativa.


* cómo se administra la relación comercial con cada comunidad.



El objetivo del dominio es **proteger las reglas del negocio**, asegurar la operación segura del fraccionamiento y mantener una administración clara y trazable.

---

## 2. Áreas funcionales del dominio

Estas áreas representan los **macro‑espacios conceptuales** del negocio.

No son *bounded contexts* todavía; son zonas naturales del dominio.

### A. Accesos y Control Operativo

Gestiona el flujo de entrada y salida de personas y vehículos.
Incluye conceptos como:

* acceso.


* invitación.


* visitante.


* guardia.


* registro de actividad.



### B. Gestión de Comunidad

Administra la estructura física y organizativa del fraccionamiento.
Incluye conceptos como:

* comunidad.


* vivienda.


* residente.


* zonas y puertas.


* parámetros operativos.



### C. Identidad y Usuarios

Gestiona la identidad digital de los usuarios y sus permisos.
Incluye conceptos como:

* usuario.


* rol.


* permisos.


* alcance dentro de la comunidad.



### D. Reglas y Políticas

Define las reglas internas que afectan accesos, permisos y comportamiento operativo.
Incluye conceptos como:

* regla.


* condición.


* política.


* suspensión.



### E. Notificaciones y Comunicación

Gestiona la comunicación hacia residentes y visitantes.
Incluye conceptos como:

* notificación.


* mensaje.


* destinatario.



### F. Auditoría y Observabilidad

Registra eventos relevantes y permite generar reportes operativos.
Incluye conceptos como:

* evento de auditoría.


* métrica.


* reporte.



### G. Administración Comercial

Gestiona la relación comercial entre Axolote y cada comunidad.
Incluye conceptos como:

* comunidad (alta).


* plan.


* estado operativo.



---

## 3. Relaciones conceptuales entre áreas

Las áreas funcionales se relacionan de forma natural:

* Las invitaciones dependen de la identidad del residente.


* Los accesos dependen de invitaciones, reglas y permisos.


* La comunidad define parámetros que afectan accesos.


* Las reglas afectan permisos y accesos.


* Las notificaciones se generan a partir de eventos del dominio.


* La auditoría registra eventos de todas las áreas.


* La administración comercial crea comunidades y asigna administradores.


* El estado de la **Administración Comercial** condiciona y puede degradar por completo la operación de la **Comunidad** y sus **Accesos** en caso de morosidad.



Estas relaciones son **conceptuales**, no técnicas.

---

## 4. Principios del negocio

El dominio de AxolPass se rige por principios que aseguran claridad y coherencia:

* **A. Claridad operativa:** Las reglas deben ser comprensibles para residentes, guardias y administradores.


* **B. Consistencia del estado:** La información de residentes, viviendas y accesos debe ser coherente en todo momento.


* **C. Trazabilidad:** Las acciones relevantes deben quedar registradas para fines operativos y de seguridad.


* **D. Privacidad:** La información personal debe manejarse de forma responsable y con límites claros.


* **E. Seguridad operativa:** Las decisiones de acceso deben ser confiables y basadas en reglas claras.


* **F. Separación de responsabilidades:** Cada área funcional debe tener límites conceptuales definidos.


* **G. Prioridad de la Realidad Física:** La seguridad, las emergencias y la evacuación de personas siempre tienen prioridad sobre las validaciones o restricciones lógicas del sistema.



---

## 5. Límites naturales del dominio (pre‑bounded contexts)

A partir de las áreas funcionales se identifican zonas conceptuales que luego se convertirán en *bounded contexts*:

* Invitaciones
* Accesos
* Comunidad
* Identidad
* Reglas
* Notificaciones
* Auditoría
* Administración Axolote