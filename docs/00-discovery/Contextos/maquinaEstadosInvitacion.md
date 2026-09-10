Máquina de estados para el ciclo de vida de una Invitación (`TemporaryPass`)

```plantuml
@startuml
skinparam style strictuml
skinparam state {
  BackgroundColor White
  BorderColor #2C3E50
  ArrowColor #2C3E50
}

title Máquina de Estados: TemporaryPass (Invitación) - AxolPass

state "PENDING" as Pending
state "ACTIVE" as Active
state "CONSUMED" as Consumed
state "EXPIRED" as Expired
state "REVOKED" as Revoked

[*] --> Pending : GeneratePassUseCase\n(Si es para fecha/hora futura)
[*] --> Active : GeneratePassUseCase\n(Si es para uso inmediato)

Pending --> Active : [Cron/Trigger]\nInicio de ventana de validez
Pending --> Revoked : CancelPassUseCase\nEmit: PassRevokedEvent

Active --> Consumed : ValidateEntryUseCase\nEmit: AccessGrantedEvent
Active --> Expired : ExpirePassUseCase\nEmit: PassExpiredEvent
Active --> Revoked : CancelPassUseCase\nEmit: PassRevokedEvent

Consumed --> [*]
Expired --> [*]
Revoked --> [*]

note right of Pending
  La invitación existe pero el QR/PIN 
  aún no es validable en caseta.
end note

note right of Active
  El visitante está dentro del 
  rango de tiempo permitido.
end note

note right of Consumed
  Estado terminal para invitaciones 
  de un solo uso.
end note
@enduml

```

### Detalles de los Estados y Transiciones

* **PENDING (Pendiente):** Es un estado transitorio vital. Si un residente genera un pase el lunes para una visita el viernes, el pase nace como `PENDING`. Si el guardia escanea el QR antes del viernes, el motor de reglas del dominio debe rechazarlo, ya que no ha transcurrido hacia `ACTIVE`.
* **ACTIVE (Activo):** El QR o PIN es válido para ser procesado por la controladora de la caseta.
* **CONSUMED (Consumido):** Estado terminal de éxito. Se alcanza únicamente cuando el `ValidateEntryUseCase` procesa el código exitosamente. En pases de un solo uso, esto bloquea cualquier intento de reutilización. *(Nota: Si el sistema soportara pases multi-entrada o de trabajadores de obra, este estado podría ciclar de vuelta a `ACTIVE` bajo ciertas reglas, pero para visitas estándar es terminal).*
* **EXPIRED (Expirado):** Estado terminal automatizado. Aquí es donde entra en acción el `ExpirePassUseCase` que definimos previamente, ejecutado por un *cron job* o un programador de eventos de dominio cuando la ventana de tiempo (ej. 24 horas) llega a su fin sin que el pase haya sido `CONSUMED`.
* **REVOKED (Revocado):** Estado terminal manual. Ocurre cuando el residente o el `CommunityAdmin` deciden cancelar explícitamente el pase antes de que se use o expire, emitiendo el `PassRevokedEvent` para invalidarlo inmediatamente en los dispositivos de borde de las casetas.

