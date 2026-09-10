# 🧩 Bloque 19: Integración con otros sistemas (Cámaras, ERP, etc.)

## 🎤 Entrevistas Simuladas (Stakeholders y Analista)

### 🧑‍💼 CommunityAdmin – Administrador del Fraccionamiento

**Analista:** ¿Qué tipo de sistemas externos usa actualmente el fraccionamiento?

**CommunityAdmin:** Tenemos un par de cámaras que graban la entrada y salida, pero no están integradas con nada. También usamos un sistema de pagos del mantenimiento que lleva contabilidad, algo así como un ERP básico.

**Analista:** ¿Le gustaría que AxolPass se integrara con alguno?

**CommunityAdmin:** Sería excelente si las cámaras pudieran asociar su grabación con el QR leído o con la entrada registrada. Y en cuanto al ERP, con que podamos exportar datos de usuarios o accesos ya nos sirve.

---

### 👨‍👩‍👧‍👦 ResidentMain – Usuario Principal

**Analista:** ¿Usted usa alguna otra aplicación externa relacionada con la seguridad del fraccionamiento?

**ResidentMain:** No directamente. Aunque a veces las cámaras del fraccionamiento son visibles desde una app, pero esa app es completamente aparte.

**Analista:** ¿Le gustaría que estuvieran conectadas?

**ResidentMain:** Si eso ayuda a mejorar la seguridad, sí. Pero no quiero que mezclen mi información personal con otros sistemas.

---

### 👮 Guardia de Seguridad

**Analista:** ¿Qué opina sobre la posible integración con cámaras?

**Guardia:** A veces el residente dice que su visita sí vino, pero nosotros no lo vimos. Si el QR se usa y se registra con foto o video del coche, ya no hay duda.

**Analista:** ¿Eso facilitaría su trabajo?

**Guardia:** Sí. Que se guarde automáticamente la imagen del auto o la cara del conductor cuando entra. Pero no debería depender de mí.

---

### 🛠 SystemAdmin – Axolote Solutions

**Analista:** ¿Qué opciones de integración están previstas?

**SystemAdmin:** Debemos contemplar integración con cámaras IP (para grabar y/o tomar snapshots en el momento del escaneo del QR). También con sistemas externos de administración (ERPs vecinales) para exportar residentes, pagos, usuarios activos, etc.

**Analista:** ¿Estas integraciones serían obligatorias?

**SystemAdmin:** No. Son opcionales por fraccionamiento. Algunos las usarán, otros no. Debemos diseñarlo como conectores externos desacoplados.

---

## 📝 Notas de Campo del Analista

- Se identifican dos integraciones principales:
  - **Cámaras de vigilancia** (para capturar imagen o video al momento del acceso).
  - **ERPs o sistemas administrativos vecinales** (para sincronizar usuarios o exportar reportes).
- La integración con cámaras debe ser **automática, no manual**, al momento del escaneo del QR.
- La integración con sistemas administrativos será **unidireccional al inicio**: AxolPass exporta datos.
- **No se debe mezclar información personal sensible sin autorización.**
- La arquitectura debe prever **conectores externos desacoplados**, para habilitar o deshabilitar estas integraciones según el fraccionamiento.

---

## 📚 Narrativa Funcional Extendida

El módulo de **Integración con Sistemas Externos** en AxolPass contempla la posibilidad de conectar el sistema con dos tipos principales de soluciones de terceros: **cámaras de videovigilancia** y **sistemas administrativos (ERPs comunitarios)**.

### Integración con Cámaras

Cuando un visitante utiliza un **código QR** para ingresar al fraccionamiento, el sistema puede enviar una señal a una cámara IP ubicada en la caseta para:
- **Capturar una imagen (snapshot)** del vehículo o visitante en el momento exacto del acceso.
- **Asociar automáticamente esa imagen** con el registro de entrada en AxolPass.
- Almacenar esa evidencia como parte del historial de seguridad, consultable por el CommunityAdmin.

Este proceso debe ser **automático**, sin requerir intervención del guardia. En caso de falla, el evento sigue registrándose, pero sin imagen.

### Integración con ERPs o Sistemas Administrativos

AxolPass puede integrarse con plataformas de administración vecinal ya existentes para:
- **Exportar información de accesos, usuarios y actividad por casa.**
- **Sincronizar listas de residentes activos** si el ERP es el sistema maestro.

Estas integraciones se diseñan como **módulos conectables**, activables según las necesidades del fraccionamiento. No se requiere una integración obligatoria, y puede activarse por etapas.

Se contemplan medidas para **garantizar la privacidad de los usuarios**, evitar la duplicación de datos, y mantener auditoría sobre cualquier sincronización o exportación realizada.

Este módulo extiende la funcionalidad de AxolPass hacia un ecosistema más amplio de seguridad y administración digital, sin perder la independencia ni la simplicidad de configuración.

