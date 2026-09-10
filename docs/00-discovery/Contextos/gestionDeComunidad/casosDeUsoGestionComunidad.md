# Casos de uso.

## 1. Configuración y Parámetros Globales (Community Context)

### UC-COM-01: Configurar Reglas de Comunidad (`ConfigureCommunitySettingsUseCase`)

* **Actor:** `CommunityAdmin` (Administrador del fraccionamiento, operando desde el panel web de gestión).
* **Descripción:** Flujo fundacional que define los parámetros operativos, límites lógicos físicos y la configuración regional de un fraccionamiento. Esta configuración actúa como la "ley suprema" de la comunidad; sus valores son consultados transversalmente por otros contextos (especialmente el Contexto de Accesos) para validar reglas de negocio y aforos. Una comunidad recién aprovisionada en el sistema no puede operar, registrar modelos ni dar de alta casas hasta que esta configuración base haya sido establecida.
* **Comando de Entrada:** `ConfigureCommunityCommand`
  * `communityId` (Identificador único del fraccionamiento).
  * **Configuración Regional:**
    * `timezone` (Cadena de texto con la zona horaria, ej. "America/Mexico_City").
  * **Límites Operativos Globales:**
    * `maxVisitorParkingSpaces` (Número total de cajones físicos destinados exclusivamente a visitantes en todo el complejo).
  * **Reglas de Consumo por Casa:**
    * `maxDailyInvitationsPerHouse` (Límite global por defecto de visitas permitidas por día para una unidad privativa).
* **Salida Esperada:** Confirmación de configuración guardada/actualizada exitosamente.
* **Eventos Disparados:**
  * `CommunitySettingsUpdatedEvent` (Notifica el cambio transportando los atributos de negocio clave como el `communityId`, el nuevo `timezone` y los límites operativos actualizados. Es vital para que el Contexto de Accesos, u otros microservicios, inicialicen o invaliden sus datos locales y adopten las nuevas reglas operativas).

* **Reglas de Negocio (Invariantes):**
  * *RN-COM-01.1 - Zona Horaria Estricta:* El sistema debe validar rigurosamente que el `timezone` provisto sea un identificador válido según la base de datos estandarizada IANA (tz database). Esto garantiza que los cálculos de expiración temporal (ej. vigencia de un código QR) sean exactos independientemente de la ubicación física de los servidores en la nube.
  * *RN-COM-01.2 - Límites Operativos Positivos:* Los valores numéricos de configuración (`maxDailyInvitationsPerHouse` y `maxVisitorParkingSpaces`) deben ser estrictamente números enteros mayores o iguales a cero. Un valor de cero en las invitaciones implicaría una política temporal de "Cero Visitas" para todo el fraccionamiento.
  * *RN-COM-01.3 - Aforo Físico Global (Capacity Limit):* El sistema registrará el valor de `maxVisitorParkingSpaces` como el límite estricto de capacidad del recinto. Este valor será consumido por el Contexto de Accesos; cuando el contador de visitantes activos en el interior alcance este límite, las interfaces físicas (`KioskApp` y `GuardConsole`) deberán pausar nuevos ingresos vehiculares, arrojando una excepción de "Aforo Lleno" hasta que se registre al menos una salida.

* **Consideraciones / Edge Cases:**
  * *Actualización en Caliente (Hot Reloading):* Si el administrador modifica los límites a mitad del día (ej. reduciendo de 10 a 5 invitaciones diarias), el sistema propagará el cambio inmediatamente. Las casas que ya hubieran emitido, por ejemplo, 8 invitaciones ese día, quedarán en un estado "sobregirado" y el sistema les impedirá generar nuevas hasta el corte de medianoche, pero **no invalidará ni cancelará** las invitaciones previamente generadas.
  * *Frontera de Dominio (SaaS vs Cliente):* Parámetros relacionados con la facturación y protección de infraestructura de Axolote Solutions (como el número máximo de cuentas móviles por casa o el número máximo de casas en el fraccionamiento) **estrictamente no forman parte de este caso de uso**. Dichos límites pertenecen al Contexto de Aprovisionamiento del Sistema (`SystemContext`) y son gestionados exclusivamente por el `SuperAdmin`.

## 2. Gestión de Infraestructura y Desarrollo

### UC-COM-02: Registrar Modelo de Propiedad (`RegisterHouseModelUseCase`)

* **Actor:** `CommunityAdmin` (Administrador del fraccionamiento).
* **Descripción:** Flujo que permite definir un arquetipo o "molde" arquitectónico para las unidades privativas del fraccionamiento. Este modelo centraliza los atributos físicos compartidos. Las casas que se registren posteriormente heredarán estas reglas, lo que garantiza consistencia y facilita actualizaciones masivas si cambian las reglas de la asamblea.
* **Comando de Entrada:** `RegisterHouseModelCommand`
  * `communityAdminId` (ID del administrador, para autorización).
  * `communityId` (Identificador único del fraccionamiento).
  * `modelName` (Nombre comercial o identificador del modelo, ej. "Zafiro", "Lote Tipo A").
  * `description` (Opcional: Descripción breve de las características).
* **Salida Esperada:** Identificador único de la entidad (`houseModelId`) y confirmación de creación exitosa.
* **Eventos Disparados:** `HouseModelRegisteredEvent`.

* **Reglas de Negocio (Invariantes):**
  * *RN-COM-02.1 - Unicidad de Nomenclatura del Modelo:* El nombre del modelo (`modelName`) debe ser único dentro del catálogo de la comunidad (`communityId`). El sistema aplicará validación de equivalencia lógica (ignorando mayúsculas, minúsculas y espacios redundantes) para evitar duplicados como "Modelo Zafiro" y "modelo zafiro ".
  * *RN-COM-02.2 - Autorización de Operación:* El sistema debe verificar que el `communityAdminId` cuente con los privilegios administrativos activos sobre el `communityId` especificado. Si no existe la relación de confianza, la operación se aborta con una excepción de seguridad (`UnauthorizedDomainOperationException`).

* **Consideraciones / Edge Cases:**
  * *Inmutabilidad vs. Evolución (Semántica de Herencia):* Para el MVP, las unidades privativas (`House`) referencian dinámicamente al `HouseModel` activo mediante su ID y consumen sus atributos vigentes en tiempo real. Si bien este caso de uso solo aborda la *creación*, el equipo de diseño debe considerar que si en el futuro se ejecuta un caso de uso de "Actualización" (ej. modificar la descripción), el cambio afectará a la visualización de *todas* las casas instanciadas con este modelo. La semántica de versionamiento histórico del modelo queda fuera del alcance actual.


### UC-COM-03: Registrar Unidad Privativa (`RegisterHouseUseCase`)

* **Actor:** `CommunityAdmin` (Administrador del fraccionamiento).
* **Descripción:** Flujo para dar de alta una nueva unidad privativa (casa, departamento o lote vacío) dentro del ecosistema del fraccionamiento. El sistema asocia esta nueva propiedad a un molde arquitectónico existente (`HouseModel`) para heredar sus límites físicos. Se reciben los identificadores internos de la propiedad (ej. manzana y número de lote) y se valida la unicidad espacial lógica. Al crearse, la propiedad nace por defecto en **estado operativo** `ACTIVE` (con derechos plenos) y "vacía" (sin residentes vinculados).
* **Comando de Entrada:** `RegisterHouseCommand`
  * `performerId` (Identificador del administrador que ejecuta la acción, para autorización y auditoría).
  * `communityId` (Identificador del fraccionamiento).
  * `houseModelId` (Identificador del prototipo arquitectónico previamente creado en UC-COM-02).
  * `blockIdentifier` (Opcional: Nomenclatura de bloque, ej. "Mza 4", "Torre A").
  * `houseNumber` (Nomenclatura específica, ej. "Lote 12", "Apto 301").
  * `internalIntercom` (Opcional: Número de extensión del interfón para la caseta).
* **Salida Esperada:** Identificador único de la entidad (`houseId`) y confirmación de creación.
* **Eventos Disparados:** `HouseRegisteredEvent` (Contiene la firma del `performerId` para el log de auditoría).

* **Reglas de Negocio (Invariantes):**
  * *RN-COM-03.1 - Autorización de Operación:* El sistema verificará que el `performerId` tenga privilegios administrativos sobre el `communityId`.
  * *RN-COM-03.2 - Integridad del Modelo Referenciado:* El `houseModelId` provisto debe existir y pertenecer estrictamente al mismo `communityId` donde se está registrando la casa. (Para evitar que un fraccionamiento use el molde de otro).
  * *RN-COM-03.3 - Unicidad Espacial Absoluta:* La combinación de `communityId`, `blockIdentifier` y `houseNumber` debe ser estrictamente única dentro del fraccionamiento.
  * *RN-COM-03.4 - Equivalencia Lógica de Nomenclatura:* La validación de unicidad (RN-COM-03.3) debe ser insensible a variaciones de formato (mayúsculas, minúsculas o espacios redundantes). Para el dominio, "Mza 4" y " mza  4 " son la misma entidad lógica y desencadenarán un rechazo por duplicidad.
  * *RN-COM-03.5 - Estado Operativo de Nacimiento:* Toda unidad privativa nueva se inicializa inamoviblemente con un estado operativo `ACTIVE` (Con derechos plenos). La restricción de la cuenta (`SUSPENDED`), ya sea por motivos de morosidad financiera o por sanciones administrativas internas del fraccionamiento, es un acto punitivo explícito que requiere la ejecución de un caso de uso separado.

* **Consideraciones / Edge Cases:**
  * *Creación Masiva (Procesamiento por Lotes):* Durante la carga inicial desde un Excel, el sistema permitirá el procesamiento masivo. Si un registro rompe la regla de unicidad o hace referencia a un `houseModelId` inexistente, el sistema capturará el error solo para esa fila, permitiendo que el resto de las unidades válidas se registren exitosamente.
  * *Ausencia de Bloques:* El sistema soporta que el `blockIdentifier` sea nulo, aplicando la regla de unicidad únicamente sobre el `houseNumber`.


### UC-COM-04: Asignar Residente Principal (`AssignPrimaryResidentUseCase`)

* **Actor:** `CommunityAdmin` (Administrador del fraccionamiento).
* **Descripción:** Crea un **vínculo activo de residencia principal** entre un usuario (`globalUserId`) y una unidad privativa (`House`), otorgándole el rol de `PrimaryResident`. Su objetivo exclusivo es registrar quién es el titular activo de la propiedad.
* **Comando de Entrada:** `AssignPrimaryResidentCommand`
* `performerId` (ID del administrador que ejecuta la acción).
* `communityId` (ID del fraccionamiento).
* `houseId` (ID de la casa).
* `globalUserId` (ID del usuario, resuelto previamente por el sistema de identidades).


* **Salida Esperada:** Identificador del vínculo de residencia (`tenancyId`) y confirmación de asignación.
* **Eventos Disparados:** `PrimaryResidentAssignedEvent`.
* **Reglas de Negocio (Invariantes):**
* *RN-COM-04.1 - Autorización:* El `performerId` debe tener privilegios de administrador sobre el `communityId`.
* *RN-COM-04.2 - Unicidad de Titularidad (Single Owner Rule):* La `House` solo puede tener una **residencia principal activa** a la vez. Si ya tiene un titular, la asignación se rechaza.
* *RN-COM-04.3 - Integridad Espacial:* La `House` debe pertenecer al `communityId`.


* **Consideraciones / Edge Cases:**
* *Orquestación:* Se asume que la interfaz de usuario (Frontend) ya se encargó de buscar o crear a la persona y obtuvo su `globalUserId` antes de llamar a este comando.

### UC-COM-05: Registrar Residente Secundario (`RegisterSecondaryResidentUseCase`)

* **Actor:** `PrimaryResident` (App Móvil) o `CommunityAdmin` (Panel Web).
* **Descripción:** Crea un **vínculo activo de residencia secundaria** entre un usuario (`globalUserId`) y una unidad privativa (`House`), otorgándole el rol de `SecondaryResident`. Permite a familiares o *roomies* generar invitaciones sin otorgar control administrativo sobre la propiedad.
* **Comando de Entrada:** `RegisterSecondaryResidentCommand`
  * `performerId` (ID del usuario o administrador que ejecuta la acción).
  * `houseId` (ID de la casa).
  * `globalUserId` (ID del nuevo residente secundario, resuelto previamente por el sistema de identidades).
* **Salida Esperada:** Identificador del vínculo de residencia (`tenancyId`) y confirmación de registro.
* **Eventos Disparados:** `SecondaryResidentRegisteredEvent`.

* **Reglas de Negocio (Invariantes):**
  * *RN-COM-05.1 - Autorización Delegada:* El `performerId` debe ser el `PrimaryResident` actual de la casa, o un `CommunityAdmin` con privilegios sobre el fraccionamiento.
  * *RN-COM-05.2 - Estado Operativo:* La `House` debe tener un `OperationalStatus` de `ACTIVE`.
  * *RN-COM-05.3 - Prevención de Duplicidad:* El `globalUserId` no puede estar ya vinculado a la misma `House` (ni como primario ni como secundario).
  * *RN-COM-05.4 - Integridad del Titular:* La `House` debe tener obligatoriamente un `PrimaryResident` asignado para poder admitir residentes secundarios.

* **Consideraciones / Edge Cases:**
  * *Orquestación:* Se asume que el Frontend ya resolvió o creó el `globalUserId` interactuando con el Contexto IAM antes de invocar este comando.
  * *Límite de Cuentas por Casa (Invariante Comercial):* El sistema deberá validar que la cantidad de residentes activos en la `House` no supere el límite `maxAccountsPerHouse` definido por Axolote Solutions en el contrato (Contexto de Suscripción). Si se excede, se rechazará la petición indicando: "Se ha alcanzado el límite máximo de cuentas permitidas para esta casa según su plan contratado".

### UC-COM-06: Remover Residente (`RemoveResidentUseCase`)

* **Actor:** `CommunityAdmin` (Panel Web) o `PrimaryResident` (App Móvil).
* **Descripción:** Desactiva (rompe) el **vínculo de residencia** (`tenancyId`) entre un usuario (`globalUserId`) y una unidad privativa (`House`), revocando instantáneamente sus derechos operativos en el fraccionamiento. 
* **Comando de Entrada:** `RemoveResidentCommand`
  * `performerId` (ID del usuario o administrador que ejecuta la acción).
  * `houseId` (ID de la casa).
  * `targetUserId` (ID del residente que será removido).
* **Salida Esperada:** Confirmación de remoción exitosa.
* **Eventos Disparados:** `ResidentRemovedEvent` (Crítico: El Contexto de Accesos debe escuchar este evento para cancelar automáticamente todas las invitaciones futuras generadas por este usuario, y el Contexto IAM para revocar sus tokens de sesión activa. *Nota sobre la emisión:* En caso de una remoción en cascada (RN-COM-06.2), el sistema emitirá eventos individuales independientes por cada usuario secundario removido, además del primario. Esto garantiza que los contextos consumidores no necesiten conocer la jerarquía de la casa para procesar las bajas).

* **Reglas de Negocio (Invariantes):**
  * *RN-COM-06.1 - Jerarquía de Remoción:* Un `CommunityAdmin` puede remover a cualquier residente (Primario o Secundario). Un `PrimaryResident` **solo** puede remover a los `SecondaryResident` asociados a su misma `houseId`.
  * *RN-COM-06.2 - Efecto Cascada (Evicción Total):* Si el administrador remueve al `PrimaryResident`, el sistema debe remover automáticamente en cascada a todos los `SecondaryResident` vinculados a esa misma casa, devolviendo la `House` a su estado "vacío".
  * *RN-COM-06.3 - Integridad del Vínculo:* El sistema debe validar que el `targetUserId` efectivamente tenga un **vínculo de residencia activo** en el `houseId` especificado antes de intentar borrarlo.

* **Consideraciones / Edge Cases:**
  * *Soft Delete vs Hard Delete:* Fiel a las buenas prácticas de auditoría, este caso de uso no hace un "DELETE" real en la base de datos de PostgreSQL. Se hace un borrado lógico (Soft Delete) marcando el `tenancyId` como inactivo o con una fecha de fin (`endDate`), para que el historial de quién habitó la casa hace un año no se pierda.


## 4. Mantenimiento y Operaciones Masivas

### UC-COM-07: Modificar Estado Operativo de Unidad Privativa (`ChangeHouseOperationalStatusUseCase`)

* **Actor:** `CommunityAdmin` (Panel Web).
* **Descripción:** Flujo punitivo o administrativo que altera los derechos de servicio de una `House` específica. Permite transicionar el estado de la propiedad entre `ACTIVE` y `SUSPENDED`. Al suspender una casa, se bloquea instantáneamente la capacidad de todos sus residentes (Primarios y Secundarios) para generar nuevas invitaciones, y la caseta rechazará cualquier intento de ingreso de visitas previamente programadas.
* **Comando de Entrada:** `ChangeHouseOperationalStatusCommand`
  * `performerId` (ID del administrador que ejecuta la acción).
  * `houseId` (ID de la casa afectada).
  * `targetStatus` (El nuevo estado operativo: `ACTIVE` o `SUSPENDED`. Nota: `INACTIVE` no está permitido en este flujo).
  * `reason` (Texto libre obligatorio al suspender, ej. "Adeudo de mantenimiento marzo" o "Multa por daños a casa club").
* **Salida Esperada:** Confirmación de actualización exitosa.
* **Eventos Disparados:**
  * `HouseOperationalStatusChangedEvent` (Evento de integración crítico. Contiene el `houseId`, el `previousStatus` y el `newStatus`. Se dispara hacia el bus de mensajes para que otros contextos reaccionen).
  * `HouseSuspendedNotificationEvent` (Capturado por el adaptador móvil para enviar una alerta Push al residente: "Su unidad ha sido suspendida administrativamente. Sus accesos han sido revocados").

* **Reglas de Negocio (Invariantes - Transaccionales):**
  * *RN-COM-07.1 - Efecto Estricto de Propagación:* El cambio de estado a `SUSPENDED` únicamente actúa como una bandera operativa en la entidad `House`. Es responsabilidad estricta de los dominios suscriptores (específicamente el Contexto de Accesos) reaccionar al evento de cambio de estado para invalidar transacciones y QRs pendientes.
  * *RN-COM-07.2 - Autorización de Operación:* El `performerId` debe tener privilegios administrativos sobre el fraccionamiento al que pertenece el `houseId`.
  * *RN-COM-07.3 - Justificación de Castigo:* Si el `targetStatus` es `SUSPENDED`, el campo `reason` no puede estar vacío. Este motivo se mostrará en la interfaz del residente y en la pantalla del guardia (`GuardConsole`) para evitar fricciones innecesarias en la caseta.
  * *RN-COM-07.4 - Transición Lógica:* El sistema ignorará silenciosamente el comando si el `targetStatus` es igual al estado actual de la casa (ej. intentar suspender una casa ya suspendida).

* **Consideraciones / Edge Cases:**
  * *Comunicación Asertiva:* Por UX, al ejecutarse una suspensión, el adaptador de notificaciones debería enviar un correo o SMS automático al `PrimaryResident` con el `reason`, para que se entere antes de que su visita se quede atrapada en la pluma.
  * *Estado Inactivo (Borrado Lógico):* Para el MVP, las transiciones operativas de `House` soportadas por Gestión de Comunidad son únicamente `ACTIVE` ↔ `SUSPENDED`. El estado `INACTIVE` queda reservado para futuras capacidades de baja lógica (ej. por fusión de lotes o corrección de captura) y se delega a herramientas de soporte técnico directo a base de datos.


### UC-COM-08: Generar Bloque de Unidades Privativas (`GenerateHouseBlockUseCase`)

* **Actor:** `CommunityAdmin` (Panel Web).
* **Descripción:** Flujo de conveniencia (Bulk Operation) diseñado para el *onboarding* inicial o expansión del fraccionamiento. Permite instanciar múltiples unidades privativas de manera automatizada mediante la definición de un rango numérico secuencial. Todas las casas generadas en este lote heredarán el mismo molde arquitectónico (`HouseModel`) y nacerán vacías, con un estado operativo `ACTIVE`.
* **Comando de Entrada:** `GenerateHouseBlockCommand`
  * `performerId` (ID del administrador que ejecuta la acción).
  * `communityId` (ID del fraccionamiento).
  * `houseModelId` (ID del prototipo arquitectónico a aplicar al bloque).
  * `blockIdentifier` (Opcional: Nomenclatura agrupadora, ej. "Mza 4", "Torre A").
  * `prefix` (Opcional: Prefijo de texto para el número, ej. "Lote ", "Depto ").
  * `startNumber` (Número inicial del rango, ej. 100).
  * `endNumber` (Número final del rango, ej. 150).
* **Salida Esperada:** Objeto `BulkGenerationResult` (Contiene la cantidad de casas creadas exitosamente, la lista de `houseId` generados y, si aplica, los nombres de las casas que fallaron por colisión).
* **Eventos Disparados:** `HouseRegisteredEvent` (El sistema despachará un evento individual por cada casa creada exitosamente dentro del bloque para mantener la atomicidad en el log de auditoría).

* **Reglas de Negocio (Invariantes):**
  * *RN-COM-08.1 - Autorización e Integridad:* El `performerId` debe ser administrador activo, y el `houseModelId` debe pertenecer al `communityId`.
  * *RN-COM-08.2 - Sentido del Rango:* El `endNumber` debe ser estrictamente mayor o igual al `startNumber`.
  * *RN-COM-08.3 - Límite de Transacción (Safeguard):* Para proteger los recursos de la base de datos (y evitar *Timeouts* en la API), la diferencia entre el rango final e inicial no podrá exceder un límite predefinido por el sistema (ej. máximo 200 casas por bloque). Si el fraccionamiento tiene 500 casas, el administrador deberá ejecutar el comando 3 veces.
  * *RN-COM-08.4 - Tolerancia a Fallos (Partial Success):* Si durante la generación de un bloque de 50 casas, el sistema detecta que el "Lote 125" ya existe en la base de datos (violando la unicidad espacial del UC-COM-03), el caso de uso **no hará un rollback** completo. Creará las 49 casas válidas, omitirá la duplicada, y la reportará como "fallida/omitida" en el `BulkGenerationResult` de salida para informar al administrador.

* **Consideraciones / Edge Cases:**
  * *Formateo de Ceros a la Izquierda (Zero-padding):* A nivel de interfaz (Frontend), se debe permitir especificar si el rango requiere ceros a la izquierda (ej. "001" al "010"). El backend simplemente concatenará el `prefix`, el número formateado y el `blockIdentifier` para validar la unicidad.
  * *Prevención de Spam:* En la UI, este comando debe tener una advertencia clara o un diálogo de confirmación (ej. "¿Estás seguro de crear 50 casas?"), ya que deshacer una generación masiva accidental requeriría un futuro caso de uso de baja masiva de casas, el cual se encuentra fuera del alcance del MVP.


## 5. Integración Sistémica y Auditoría

### UC-COM-09: Inicializar Entorno de Comunidad (`InitializeCommunityEnvironmentUseCase`)

* **Actor:** Sistema (Invocado automáticamente por el Bus de Eventos).
* **Descripción:** Flujo de infraestructura (System-to-System). Reacciona al evento `CommunityProvisionedEvent` emitido por el Contexto de Suscripción. Su objetivo es crear el registro raíz de la comunidad en la base de datos local del Contexto de Comunidad, estableciendo su identificador único y su capacidad máxima inmutable. Sin este paso, el administrador local no tendría un espacio donde empezar a trabajar.
* **Comando de Entrada:** `InitializeCommunityCommand` (Construido a partir del *payload* del evento).
  * `communityId` (El ID generado por Suscripción, ej. `C-999`).
  * `communityName` (El nombre comercial, ej. "Las Bugambilias").
  * `totalCapacity` (El límite máximo de casas permitidas).
* **Salida Esperada:** Creación del registro raíz de la `Community`.
* **Eventos Disparados:** Ninguno a nivel de dominio (Es un caso de uso puramente reactivo que asienta datos locales).

* **Reglas de Negocio (Invariantes):**
  * *RN-COM-09.1 - Idempotencia Estricta:* Si por un error de red el evento llega dos veces, el sistema verificará si el `communityId` ya existe. Si existe, ignorará el mensaje para no duplicar el fraccionamiento ni sobrescribir datos.
  * *RN-COM-09.2 - Adopción de Capacidad:* El valor de `totalCapacity` recibido se guardará como un límite duro en el esquema de Comunidad. (Este es el valor que el `UC-COM-03` y `UC-COM-08` consultarán en el futuro para saber si el administrador ya llenó su fraccionamiento y bloquearle la creación de más casas).
  * *RN-COM-09.3 - Estado de Pendiente de Configuración:* La comunidad nace en un estado `PENDING_SETUP`. El administrador local podrá iniciar sesión (gracias a IAM), pero no podrá operar la caseta ni crear casas hasta que ejecute manualmente el **`UC-COM-01: Configurar Reglas de Comunidad`** (para definir la zona horaria y los aforos vehiculares).
* **Consideraciones / Edge Cases:**
  * *Condición de Carrera (Eventual Consistency Latency):* Al ser una arquitectura asíncrona, el Contexto IAM podría crear la cuenta de usuario (UC-IAM-02) unos milisegundos más rápido de lo que el Contexto de Comunidad tarda en procesar este evento. Si el administrador inicia sesión inmediatamente, podría no encontrar su fraccionamiento. **Mitigación:** La interfaz (Frontend) debe estar preparada para manejar este estado, mostrando un mensaje de *"Aprovisionando su entorno, por favor espere unos segundos"* si la consulta inicial de comunidad devuelve un "No Encontrado" temporal.
  * *Fallas Transitorias y Retención (Dead Letter Queue):* Si al momento de recibir el evento, la base de datos local del Contexto de Comunidad está reiniciándose o saturada, el mensaje no debe perderse. El *Message Broker* (ej. RabbitMQ/Kafka) aplicará una política de reintentos (Retry Pattern) y, de fallar repetidamente, enviará el evento a una cola de mensajes muertos (DLQ - Dead Letter Queue) disparando una alerta crítica al equipo de DevOps de Axolote Solutions para su procesamiento manual.
  * *Validación de Integridad del Payload:* Si el evento llega con un formato corrupto (ej. falta el `totalCapacity` por un bug en el despliegue del Contexto de Suscripción), el caso de uso abortará la creación, registrará el error en el log centralizado y enviará el mensaje directo a la DLQ, evitando crear un fraccionamiento con datos inconsistentes.
  * *Referencia Arquitectónica:* Por favor referirse a [ADR-015](../../adr/ADR-015-estrategia-resiliencia-eventos-asincronos.md)


### UC-COM-10: Auditar Identidad de Visitante (Break-Glass Flow)

* **Actor:** `CommunityAdmin` (Administrador del fraccionamiento, operando desde el panel web de gestión).
* **Descripción:** Flujo transaccional de excepción. Por defecto, la bitácora de accesos muestra los nombres y placas de los visitantes de forma anonimizada para proteger la privacidad. Este caso de uso permite al administrador vulnerar ese velo de privacidad para un registro específico ante un incidente de seguridad o daños a la propiedad. El sistema exige una justificación obligatoria, revela los datos y deja un rastro inmutable de la acción.
* **Comando de Entrada:** `RevealVisitorIdentityCommand`
  * `adminId` (ID del administrador que ejecuta la acción).
  * `accessRecordId` (ID del registro de acceso o invitación que se desea investigar).
  * `justificationReason` (Texto obligatorio describiendo el motivo legal u operativo de la consulta, mínimo 20 caracteres).
* **Salida Esperada:** Objeto con los datos de identidad revelados (`guestName`, `licensePlate`, `vehicleDescription`).
* **Eventos Disparados:**
  * `PrivacyOverrideAuditedEvent`: Registra de forma permanente en la bitácora global que el administrador rompió el protocolo de privacidad para ese registro específico y guarda su justificación.

* **Reglas de Negocio (Invariantes - Transaccionales):**
  * *RN-COM-10.1 - Justificación Obligatoria:* El comando será rechazado inmediatamente (`HTTP 400 Bad Request`) si el campo `justificationReason` está vacío o no cumple con la longitud mínima de caracteres. Esto evita que se introduzcan textos evasivos o flojos como "test", "revisión" o "x".
  * *RN-COM-10.2 - Trazabilidad Transaccional Inmutable:* El sistema debe persistir el evento de auditoría en el `AuditLog` dentro de la misma transacción de la base de datos en la que se liberan los datos sensibles. Si la escritura del rastro de auditoría falla por cualquier problema técnico, la petición completa hace rollback y los datos del visitante jamás se muestran en pantalla.
