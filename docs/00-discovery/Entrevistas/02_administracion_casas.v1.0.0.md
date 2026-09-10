# Bloque 2 — Administración de Casas y Residentes

## Entrevista Simulada (Stakeholder: CommunityAdmin de un fraccionamiento)

Analista: Gracias por reunirte conmigo. Empecemos por lo básico. ¿Quién gestiona la información de las casas dentro del fraccionamiento?

CommunityAdmin: Yo me encargo directamente desde la aplicación. Una vez que el fraccionamiento es dado de alta por Axolote Solutions, lo primero que hago es registrar las casas.

Analista: ¿Y cómo se realiza ese registro? ¿Es manual?

CommunityAdmin: Depende del fraccionamiento. En algunos casos, las casas están numeradas de forma continua, como del 1 al 150. Para esos casos estaría bien contar con una opción para generarlas automáticamente. Pero en otros, la numeración es por bloques, como del 100 al 120, 200 al 220, etc. Y en otros, simplemente son números irregulares, y ahí sí hay que hacerlo uno por uno.

Analista: ¿Qué información mínima necesitas para dar de alta una casa?

CommunityAdmin: El número o nombre de la casa, su estado (si está activa o suspendida) y el residente principal. También pueden agregarse residentes secundarios, como familiares o personal de confianza.

Analista: ¿Puede una casa quedar sin residentes?

CommunityAdmin: Sí, especialmente si está vacía por temas de renta o venta. El fraccionamiento puede tener contacto con el propietario para que se mantenga al tanto de las cuotas, pero en el sistema puede quedar vacía temporalmente.

Analista: ¿Qué control tienes sobre los residentes?

CommunityAdmin: Yo asigno al residente principal de cada casa. Él a su vez puede agregar usuarios secundarios. Si es necesario, puedo suspender una casa y con eso se bloquea la generación de invitaciones y las notificaciones. También puedo eliminar a todos los usuarios de una casa si, por ejemplo, terminó el contrato de renta.

Analista: ¿Qué ocurre cuando cambia el residente principal?

CommunityAdmin: Debo eliminar al anterior y luego asignar al nuevo. Con eso, todas sus invitaciones se invalidan automáticamente. Lo importante es que el historial se conserve por temas de trazabilidad.

Analista: ¿Axolote Solutions interviene en esta gestión?

CommunityAdmin: No debería. Este es un proceso autónomo del fraccionamiento. Pero quizás sería útil que tuvieran acceso a un panel de supervisión o auditoría, por si acaso.

Analista: ¿Puedes reactivar casas suspendidas?

CommunityAdmin: Sí, sin intervención de Axolote.

Analista: ¿Cuántos residentes se permiten por casa?

CommunityAdmin: Creo que un límite sería útil. Tal vez 4, para evitar abusos.

⸻

## Notas de Campo del Analista (Bloque 2)
	•	El CommunityAdmin es responsable de gestionar el ciclo de vida completo de las casas: alta, suspensión, reactivación, y eliminación de residentes.
	•	Existe una variabilidad importante en la numeración de casas, por lo que deben ofrecerse mecanismos flexibles de carga (automática, por bloques, manual).
	•	Las casas pueden estar temporalmente desocupadas; es importante reflejar ese estado en el sistema sin perder la trazabilidad.
	•	El residente principal es el único punto de entrada para gestionar usuarios secundarios.
	•	Los residentes secundarios no son visibles para el CommunityAdmin, respetando cierta privacidad.
	•	El CommunityAdmin controla el acceso y estado operativo de las casas (suspensión, reactivación, desvinculación).
	•	Eliminación de usuarios implica también la revocación automática de invitaciones.
	•	No se prevé automatización de vencimientos por casa; el control se ejerce manualmente.
	•	Historial de acciones por casa es importante, pero falta definir quién tiene visibilidad (Axolote incluido).
	•	Se sugiere un límite razonable de usuarios por casa, pero aún no hay consenso.

⸻

## Narrativa Funcional Extendida

En el día a día, el CommunityAdmin inicia la operación de un fraccionamiento cargando el listado de casas. Según el diseño del lugar, puede generar el listado automáticamente si la numeración es regular o hacerlo manualmente si es más compleja.

Cada casa es identificada por un número o nombre único, y puede encontrarse en estado activa, suspendida o vacía. La casa se asocia a un residente principal, quien tendrá privilegios para generar invitaciones y registrar usuarios secundarios.

Cuando una casa es suspendida, su residente puede seguir accediendo a la app, pero no puede operar. Esta suspensión puede deberse a razones internas del fraccionamiento, como morosidad o conflictos vecinales.

Si se termina un contrato de renta o la casa queda vacía, el CommunityAdmin puede eliminar a todos los usuarios asociados. Esta acción revoca todas las invitaciones activas y mantiene el historial para futuras auditorías.

El CommunityAdmin también puede reactivar casas y reasignar residentes. Este flujo es completamente autónomo, sin intervención de Axolote Solutions, aunque se sugiere que Axolote tenga visibilidad para propósitos de monitoreo o soporte.

El sistema debe permitir una cantidad máxima de residentes por casa, sugerida en 4, para evitar mal uso. Sin embargo, se debe permitir flexibilidad a futuro.

