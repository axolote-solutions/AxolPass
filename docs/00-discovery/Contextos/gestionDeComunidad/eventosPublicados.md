```markdown
# Catálogo de Eventos Publicados: Contexto de Comunidad

Este documento define los contratos técnicos (esquemas) de los eventos de dominio que el Contexto de Comunidad publica hacia el Message Broker (ej. RabbitMQ/Kafka) para que otros contextos los consuman.

---

## `CommunitySettingsUpdatedEvent`

* **Descripción:** Se emite cuando el administrador del fraccionamiento configura o actualiza las reglas operativas base (como aforos y zona horaria) de su comunidad.
* **Topic / Routing Key:** `community.settings.updated`
* **Productores:** Contexto de Comunidad (`UC-COM-01: Configurar Reglas de Comunidad`).
* **Consumidores Conocidos:** Contexto de Accesos (`UC-ACC-19: Inicializar Entorno de Caseta`).

### Esquema del Payload (JSON)

```json
{
  "eventId": "e9b2c8a4-1d3f-4e5a-8b7c-9a0b1c2d3e4f",
  "eventType": "CommunitySettingsUpdatedEvent",
  "occurredOn": "2026-04-02T10:33:00Z",
  "payload": {
    "communityId": "C-999",
    "timezone": "America/Mexico_City",
    "maxVisitorParkingSpaces": 50,
    "maxDailyInvitationsPerHouse": 10
  }
}
```

### Diccionario de Datos

| Campo | Tipo | Descripción |
| :--- | :--- | :--- |
| `eventId` | UUID | Identificador único y universal del evento (para idempotencia). |
| `eventType` | String | Nombre exacto del evento para enrutamiento interno. |
| `occurredOn` | String | Timestamp en formato ISO-8601 (UTC) del momento exacto de la actualización. |
| `payload.communityId` | String | Identificador único del fraccionamiento que sufrió el cambio. |
| `payload.timezone` | String | Identificador de zona horaria válido según IANA (ej. `America/Mexico_City`). |
| `payload.maxVisitorParkingSpaces` | Integer | Nuevo límite de aforo físico para la caseta. |
| `payload.maxDailyInvitationsPerHouse` | Integer | Límite de pases diarios por unidad privativa. |


