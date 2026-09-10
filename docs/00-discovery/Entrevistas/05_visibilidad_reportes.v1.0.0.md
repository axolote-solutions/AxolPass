# 🧩 Bloque 5: Visibilidad y Reportes para Administradores

---

## 🎤 Entrevistas Simuladas (Stakeholders y Analista)

### 🧑‍💼 CommunityAdmin – Administrador del Fraccionamiento

**Analista:** ¿Qué información necesita consultar regularmente?  
**CommunityAdmin:** Lo más importante son los accesos: quién entra, quién sale, cuándo, en qué casa. También queremos ver si una casa genera muchas invitaciones, o si hay movimientos raros.

**Analista:** ¿Con qué frecuencia consulta esos datos?  
**CommunityAdmin:** Diario y semanalmente. A veces por incidentes específicos. También generamos reportes mensuales para la mesa directiva del fraccionamiento.

**Analista:** ¿Qué tipo de filtros necesita?  
**CommunityAdmin:** Por fecha, casa, tipo de usuario (residente, visitante, proveedor), método de acceso (QR, manual), estado (válido, fallido, ya usado, etc.).

**Analista:** ¿Desean exportar los reportes?  
**CommunityAdmin:** Sí, en Excel o PDF. A veces nos los piden para asambleas o auditorías externas.

---

### 🛠 SystemAdmin – Axolote Solutions

**Analista:** ¿Qué alcance tienen los reportes en su plataforma?  
**SystemAdmin:** Son fundamentales para la transparencia del sistema. Nos interesa que los registros estén completos, con trazabilidad, y listos para auditoría.

**Analista:** ¿Qué funcionalidades avanzadas se consideran?  
**SystemAdmin:** Gráficas por casa, estadísticas de uso, detección de picos de tráfico, alertas por comportamiento atípico (ej. muchas visitas nocturnas o invitaciones fallidas).

**Analista:** ¿Quién puede ver estos reportes?  
**SystemAdmin:** Solo CommunityAdmin y soporte de Axolote. No los residentes. La privacidad es importante.

---

## 📝 Notas de Campo del Analista

- El CommunityAdmin necesita **consultar registros filtrados** y **generar reportes exportables**.
- Se requieren filtros por:
  - Fecha
  - Casa
  - Tipo de usuario (residente/visitante)
  - Método de acceso (QR/manual)
  - Resultado de validación
- Los reportes deben poder exportarse en **PDF y Excel**.
- Es deseable incluir **gráficas y métricas agregadas**: accesos por día, por casa, por tipo de usuario.
- El módulo **no está disponible para residentes**, únicamente para administradores y Axolote.
- La plataforma podría incluir **alertas automatizadas** por comportamientos inusuales.
- Se espera que los reportes sean usados en **asambleas, auditorías, y decisiones de seguridad**.

---

## 📚 Narrativa Funcional Extendida

El módulo de **Visibilidad y Reportes para Administradores** es una herramienta clave en AxolPass para garantizar la **gestión transparente y eficaz de la seguridad comunitaria**. Está diseñado exclusivamente para los **administradores del fraccionamiento (CommunityAdmin)** y, en situaciones específicas, para el equipo de soporte de **Axolote Solutions**.

Desde este módulo, los administradores pueden **consultar, filtrar, y generar reportes** relacionados con:
- Entradas y salidas
- Invitaciones generadas
- Usuarios por casa
- Frecuencia de visitas
- Registros con errores o accesos manuales

Los filtros permiten búsquedas específicas por:
- **Fechas**
- **Casa**
- **Tipo de acceso** (residente, visitante, proveedor)
- **Método** (QR, manual)
- **Resultado** (válido, inválido, expirado, etc.)

El sistema ofrece visualización en tiempo real de los registros, así como la posibilidad de **generar reportes en Excel o PDF**, útiles para reuniones internas o requerimientos legales.

Además, se contempla una **vista de estadísticas y gráficas**, donde los administradores pueden detectar:
- Días y horarios con más tráfico
- Casas con mayor volumen de visitas
- Porcentaje de accesos manuales vs. automáticos
- Incidencias recurrentes

Por seguridad y privacidad, **los residentes no tienen acceso a esta información**, y solo personal autorizado podrá visualizar los datos.

Finalmente, el módulo podrá incluir **sistemas de alerta**, que notifiquen automáticamente al administrador sobre:
- Intentos fallidos de acceso múltiples
- Invitaciones no utilizadas
- Patrones anómalos de movimiento

Este módulo fortalece la capacidad de los administradores para **tomar decisiones informadas**, elevar el nivel de **prevención en seguridad**, y garantizar un **control claro sobre el funcionamiento del fraccionamiento**.
