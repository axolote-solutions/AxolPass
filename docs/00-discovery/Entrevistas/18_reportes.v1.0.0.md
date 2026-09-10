# 🧩 Bloque 18: Reportes y Exportación de Información

## 🎤 Entrevistas Simuladas (Stakeholders y Analista)

### 🧑‍💼 CommunityAdmin – Administrador del Fraccionamiento

**Analista:** ¿Qué tipo de reportes considera útiles para la administración?

**CommunityAdmin:** Principalmente quiero reportes de accesos: quién entró, a qué hora, si fue visitante o residente, y a qué casa iba. También reportes de incidentes, si hubo accesos fallidos, QR expirados, entradas forzadas, etc.

**Analista:** ¿Le interesan reportes por fecha?

**CommunityAdmin:** Claro, que yo pueda filtrar por día, semana o mes. También por casa, por usuario, o por tipo de acceso.

**Analista:** ¿Desea exportar esta información?

**CommunityAdmin:** Sí, en Excel o PDF. A veces lo necesito para presentar al comité vecinal.

---

### 👨‍👩‍👧‍👦 ResidentMain – Usuario Principal

**Analista:** ¿Qué información espera ver usted como residente?

**ResidentMain:** Me gustaría ver un historial de entradas de mis invitados. Saber si entraron, cuándo, y si hubo problemas. También cuántas invitaciones he generado en el mes.

**Analista:** ¿Necesita exportarlo?

**ResidentMain:** No es necesario para mí. Pero sí quiero verlo claro en la app.

---

### 👮 Guardia de Seguridad

**Analista:** ¿Los reportes le ayudarían a usted también?

**Guardia:** Para nosotros sería útil un resumen diario: cuántos accesos hubo, cuántos se hicieron manualmente, cuántos fueron por QR, y si hubo incidentes.

**Analista:** ¿Y cómo le gustaría verlos?

**Guardia:** En una pantalla en la caseta, algo rápido. No necesito exportar nada.

---

### 🛠 SystemAdmin – Axolote Solutions

**Analista:** ¿Qué necesidades tienen desde el punto de vista del sistema?

**SystemAdmin:** Necesitamos que los reportes puedan generarse de forma eficiente, sin afectar el rendimiento. Y deben respetar las reglas de acceso: un CommunityAdmin no puede ver datos de otro fraccionamiento, por ejemplo.

**Analista:** ¿La exportación a formatos externos debe estar auditada?

**SystemAdmin:** Sí. Si alguien exporta un reporte, debe quedar registrado quién lo hizo, cuándo, y qué tipo de información.

---

## 📝 Notas de Campo del Analista

- Los reportes son especialmente importantes para **CommunityAdmin** y deben incluir:
  - Historial de accesos (por fecha, usuario, casa)
  - Fallos de acceso, incidentes, registros manuales
  - Actividad de residentes y visitas

- El **residente** solo requiere visualización en app, no exportación.
- El **guardia** requiere un resumen visual simple (por día).
- Los reportes deben poder **filtrarse por múltiples criterios**: fecha, casa, usuario, tipo de acceso.
- Exportación disponible en **Excel y PDF** (solo para CommunityAdmin).
- Se requiere **auditoría de exportación** para trazabilidad.
- El sistema debe aplicar correctamente las reglas de visibilidad y acceso.

---

## 📚 Narrativa Funcional Extendida

El módulo de **Reportes y Exportación de Información** de AxolPass permite generar, consultar y exportar datos relevantes sobre el comportamiento del sistema, especialmente relacionado con accesos y eventos registrados.

El **CommunityAdmin** necesita acceso a reportes detallados sobre:
- Entradas de visitantes y residentes
- Fallas de acceso (QR expirado, inválido, ya usado)
- Eventos registrados manualmente por guardias
- Estadísticas de uso (número de invitaciones por casa, accesos por tipo, etc.)

Estos reportes deben poder filtrarse por:
- Rango de fechas
- Casa o número de vivienda
- Usuario que generó la invitación
- Tipo de acceso (QR, manual, emergencia)

Los reportes pueden visualizarse en el sistema y, para el CommunityAdmin, exportarse en formato **Excel o PDF**, con encabezados claros y diseño preparado para presentación ante el comité vecinal.

Toda exportación de datos será **auditada**: se registra qué usuario la realizó, en qué fecha y hora, y qué filtros aplicó.

Por su parte, los **residentes** solo requieren ver su propio historial de invitaciones y accesos en la app, sin necesidad de exportación.

El **guardia** de seguridad accede a un panel diario donde visualiza de forma rápida:
- Número total de accesos
- Accesos por tipo (QR, manual)
- Incidentes reportados

Este módulo refuerza la **transparencia**, **control** y **seguimiento** de todos los movimientos relevantes en el sistema, sin invadir la privacidad de los usuarios ni comprometer la seguridad de los datos.

