# 🧩 Bloque 9: Trazabilidad, Auditoría y Registros

---

## 🎤 Entrevistas Simuladas (Stakeholders y Analista)

### 👨‍💼 CommunityAdmin

**Analista:** ¿Qué tan importante es para ustedes contar con registros de acceso, invitaciones y eventos?  
**CommunityAdmin:** Es fundamental. Queremos poder revisar quién entró, cuándo, a qué casa iba, y si hubo problemas al momento del acceso. En especial cuando hay conflictos o quejas entre vecinos.

**Analista:** ¿Quién debería tener acceso a esa información?  
**CommunityAdmin:** Solo nosotros como administradores del fraccionamiento. Los residentes no deben ver información que no les compete.

**Analista:** ¿Qué tipo de reportes les serían útiles?  
**CommunityAdmin:** Historial de accesos por casa, estadísticas de invitaciones generadas, cantidad de accesos rechazados o con problemas. Y si se puede, un log de eventos críticos (errores, suspensiones, etc.).

---

### 🛠 SystemAdmin (Axolote Solutions)

**Analista:** ¿Cómo manejan ustedes la trazabilidad desde el sistema central?  
**SystemAdmin:** Todo debe quedar registrado: accesos, fallas del lector, eventos manuales del guardia, cambios administrativos. Necesitamos auditoría completa para investigar posibles fallas, accesos indebidos o uso anómalo del sistema.

**Analista:** ¿Quién puede acceder a esos registros?  
**SystemAdmin:** Nosotros a nivel global. Y los CommunityAdmins a nivel de su propio fraccionamiento. Pero la información debe ser bien segmentada.

**Analista:** ¿Consideran útil incluir exportación de logs o reportes?  
**SystemAdmin:** Sí, exportar en CSV o PDF para respaldos o revisiones externas sería valioso.

---

### 👮 Guardia de Seguridad

**Analista:** ¿Registran ustedes manualmente los accesos?  
**Guardia:** No. El lector lo hace. Pero si hay un problema (el QR no se lee, barrera dañada), yo registro manualmente el acceso o rechazo, y eso debe guardarse.

**Analista:** ¿Te gustaría poder revisar el historial de eventos del día?  
**Guardia:** Sí, para saber cuántos accesos hubo, cuántos rechacé y si hubo incidentes.

---

## 📝 Notas de Campo del Analista

- La trazabilidad debe cubrir:
  - Accesos exitosos y fallidos (con hora exacta y casa destino).
  - Invitaciones generadas, expiradas o reutilizadas.
  - Eventos manuales (ej. acceso forzado o apertura manual).
  - Cambios administrativos (suspensión de casas o usuarios).
- Cada rol accede solo a registros de su nivel:
  - **CommunityAdmin**: registros de su fraccionamiento.
  - **SystemAdmin**: registros globales.
  - **Guardias**: resumen operativo del día.
- Los residentes no deben tener acceso a registros ajenos.
- Debe existir una **bitácora inalterable** para fines de auditoría.
- Se contempla incluir exportación de datos en formatos como CSV o PDF.

---

## 📚 Narrativa Funcional Extendida

El módulo de **Trazabilidad, Auditoría y Registros** es esencial para la operación segura y transparente de AxolPass. Su propósito es garantizar que toda actividad relevante del sistema —desde accesos hasta cambios administrativos— quede registrada de forma clara, segura e inalterable.

Cuando un visitante accede al fraccionamiento utilizando un código QR, el sistema registra:
- Hora exacta del escaneo.
- Casa destino.
- Resultado de la validación (válido, ya usado, expirado, etc.).
- Tipo de evento (acceso automático, apertura manual, fallo técnico).

Si ocurre un problema —como un lector dañado o una apertura de emergencia— el guardia puede registrar un **evento manual**, indicando el motivo. Esto asegura que no haya pérdidas de trazabilidad ante contingencias.

Por otro lado, cuando un **administrador suspende una casa, elimina usuarios o reactiva el servicio**, estos cambios también quedan guardados en un **log administrativo con justificación obligatoria**.

Cada **CommunityAdmin** tiene acceso a los registros de su propio fraccionamiento, permitiéndole:
- Auditar el historial de accesos por casa.
- Consultar reportes de actividad.
- Detectar patrones anómalos o posibles abusos.

Los **SystemAdmins** de Axolote Solutions tienen acceso global a todos los logs, y pueden generar reportes consolidados, investigar incidentes, o realizar auditorías de operación.

Finalmente, se prevé que los registros puedan **exportarse en formato PDF o CSV**, tanto para respaldo como para presentación ante terceros si es necesario (comités, autoridades, etc.).

Este módulo refuerza la integridad operativa del sistema, la confianza de los usuarios y la responsabilidad de los administradores, siendo uno de los pilares técnicos más críticos del ecosistema AxolPass.
