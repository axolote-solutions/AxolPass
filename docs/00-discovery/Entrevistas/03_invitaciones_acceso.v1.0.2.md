# 🔹 Bloque 3: Invitaciones y Acceso

---

## 👥 Entrevista Simulada (Stakeholders y Analista)

### Stakeholder: ResidentMain

**Analista:** ¿Cómo se generan las invitaciones para visitantes?  
**ResidentMain:** Desde la app móvil, el residente principal puede generar una invitación indicando el nombre del visitante, la placa del vehículo y una hora estimada de llegada. La idea es que sea sencillo y rápido.

**Analista:** ¿Esa hora es obligatoria?  
**ResidentMain:** Sí, pero es solo referencial. No debería invalidarse la invitación si llega antes o después. Lo importante es que sea válida el día completo.

**Analista:** ¿Cómo se entrega la invitación al visitante?  
**ResidentMain:** Se genera un código QR. Yo lo puedo enviar por WhatsApp o mostrarlo desde la app. La otra persona solo tiene que mostrarlo al guardia o escanearlo en el lector.

---

### Stakeholder: CommunityAdmin

**Analista:** ¿Pueden todos los residentes crear invitaciones?  
**CommunityAdmin:** El usuario principal siempre puede. También puede habilitar a usuarios secundarios de su casa.

**Analista:** ¿Qué pasa si un visitante llega fuera de la hora indicada?  
**CommunityAdmin:** Anteriormente pensábamos que eso debía invalidar el acceso, pero ya no. El código debe seguir funcionando mientras sea el mismo día. Si es para el viernes, debe servir desde cualquier hora del viernes hasta las 00:00 del sábado.

**Analista:** ¿Qué sucede si el mismo código se intenta usar dos veces?  
**CommunityAdmin:** Si ya se usó una vez ese día, debe marcar como "ya utilizado". Pero si no se ha usado y es el día correcto, debe permitir el ingreso.

**Analista:** ¿El guardia puede ver el nombre y la placa del visitante?  
**CommunityAdmin:** Sí, pero no puede modificar nada. Solo visualizar y validar.

---

### Stakeholder: SecurityGuard

**Analista:** ¿Cómo es el proceso en la caseta?  
**SecurityGuard:** Escaneamos el código con el lector o la app. Si es válido, se abre la barrera. Si no, se muestra el motivo: QR expirado, inválido, ya usado, etc.

**Analista:** ¿Qué pasa si el QR no se puede leer?  
**SecurityGuard:** Podemos ingresar la placa manualmente o buscar por nombre. Pero eso no debería ser frecuente.

---

## 📝 Notas de Campo del Analista

- La validez de la invitación aplica para **todo el día indicado**, no por hora.
- Los códigos QR deben funcionar desde **00:00 hasta las 23:59** del día de la visita.
- **Un código QR solo puede usarse una vez.**
- Residentes secundarios pueden generar sus propias invitaciones.
- La app del guardia permite visualizar detalles, registrar accesos, e ingresar datos manualmente en casos excepcionales.
- Se contempla registrar errores de lectura o apertura manual por parte del guardia.

---

## 📚 Narrativa Funcional Extendida

En el ecosistema **AxolPass**, el proceso de generación y validación de invitaciones gira en torno a la autonomía del residente y la operatividad sencilla para los visitantes. El **residente principal** puede generar invitaciones desde la app, indicando el **nombre del visitante**, **placa del vehículo**, y una **hora estimada** de llegada.

Por diseño, **Axolote Solutions y el fraccionamiento no deben asumir el contexto exacto de cada visita**, por lo que **una invitación permanece válida durante todo el día de su emisión** (de 00:00 a 23:59). Si un visitante llega antes o después de la hora estimada, el sistema debe seguir permitiendo el acceso mientras la fecha coincida.

La invitación se representa con un **código QR único**. Este puede escanearse al llegar al acceso del fraccionamiento. Si se intenta usar fuera del día de validez, será marcado como **expirado**. Si se intenta **reutilizar un código ya utilizado**, se marcará como “ya usado”.

El sistema da al **guardia de seguridad** herramientas de validación en tiempo real, con posibilidad de ingresar datos manuales en caso de falla técnica. También permite registrar observaciones en caso de accesos forzados o errores del lector.

Esta lógica brinda flexibilidad, seguridad y una experiencia confiable para residentes, visitantes y personal de seguridad.

---
