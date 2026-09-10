# Guía de Diseño e Implementación: UC-SUS-04 (Registro de Pago Manual)

## 1. Contexto y Análisis de Complejidad Oculta
A primera vista, el registro de un pago parece una operación CRUD estándar ("Guardar un monto en la base de datos y actualizar una fecha"). Sin embargo, el análisis de negocio reveló que esta operación es el núcleo financiero del sistema. 

Se diseñó bajo principios estrictos de **Diseño Guiado por el Dominio (DDD)** para mitigar los siguientes riesgos críticos identificados durante la fase de análisis:

* **El Loophole de la Deuda Histórica:** Si la nueva fecha de vigencia se calculara sumando tiempo a la fecha *actual* (`NOW`), el sistema perdonaría automáticamente meses de deuda a los clientes morosos.
* **El Peligro de la Condición de Carrera:** Separar el registro del pago y la extensión del servicio mediante eventos asíncronos creaba el riesgo de tener "dinero huérfano" si la actualización de la fecha fallaba.
* **Violación de SRP/OCP a futuro:** Si las matemáticas financieras se codificaban dentro de la Capa de Aplicación (el Caso de Uso), futuros requerimientos (ej. *Regalar un mes de servicio por compensación*) obligarían a duplicar código.

---

## 2. Decisiones Arquitectónicas Aplicadas

Para resolver los riesgos anteriores, la implementación de este caso de uso **debe** seguir estas directrices:

1. **Patrón Unit of Work (ACID):** El ingreso del dinero y la extensión de la vigencia ocurren en una única transacción de base de datos. Es un escenario de "Todo o Nada". No hay persistencia parcial.
2. **Cálculo Basado en `paidThroughDate`:** La fórmula de vigencia obliga a que el tiempo comprado se sume a la última fecha pagada del cliente, obligándolo a cubrir la deuda cronológicamente para poder reactivar su servicio.
3. **Modelo de Dominio Rico:** El Caso de Uso actúa solo como un "Director de Orquesta". Toda la aritmética financiera y las reglas de transición de estado se delegan estrictamente a los Agregados de Dominio (`Community` y `FinancialProfile`).

---

## 3. Especificación del Caso de Uso

| Atributo | Definición |
| :--- | :--- |
| **Actor** | `SystemAdmin` |
| **Comando** | `RegisterManualPaymentCommand (communityId, amount, paymentDate, referenceNumber)` |
| **Responsabilidad** | Orquestar el registro contable y delegar la mutación de estado al dominio. |
| **Eventos a Emitir** | `SubscriptionPeriodExtendedEvent` (Siempre) <br> `SubscriptionReactivatedEvent` (Solo si el estado cambia de SUSPENDED a ACTIVE) |

### Reglas de Negocio a Implementar (Invariantes)
* **RN-01 (Unicidad):** Rechazar la operación si el `referenceNumber` ya existe.
* **RN-02 (Cero Condonaciones):** `Ciclos Cubiertos = amount / basePrice`. La nueva vigencia se calcula como: `newPaidThroughDate = currentPaidThroughDate + Ciclos`.
* **RN-03 (Transición de Estado):** La comunidad muta a `ACTIVE` **solo si** la `newPaidThroughDate` calculada supera la fecha de ejecución (`NOW`). De lo contrario, permanece `SUSPENDED` (abono parcial a la deuda).
* **RN-04 (Límite Contractual):** La operación debe abortarse si la nueva fecha supera el `contractEndDate`.

---

## 4. Implementación de Referencia (Java / Spring Boot)

El desarrollador asignado a este requerimiento debe basar su implementación en la siguiente estructura para garantizar la separación de responsabilidades (Dominio vs Aplicación).

### A. El Objeto de Valor: `FinancialProfile` (Capa de Dominio)
Agrupa las matemáticas financieras y protege los límites legales. Cerrado a modificaciones por infraestructura.

```java
public class FinancialProfile {
    private BigDecimal basePrice;
    private LocalDate paidThroughDate;
    private LocalDate contractEndDate;

    public FinancialProfile(BigDecimal basePrice, LocalDate paidThroughDate, LocalDate contractEndDate) {
        this.basePrice = basePrice;
        this.paidThroughDate = paidThroughDate;
        this.contractEndDate = contractEndDate;
    }

    public LocalDate calculateNewPaidThroughDate(BigDecimal amountPaid) {
        // La división entera define los ciclos exactos pagados. 
        // Nota Fase 2: El remanente deberá manejarse como Saldo a Favor.
        int paidCycles = amountPaid.divide(this.basePrice, 0, RoundingMode.DOWN).intValue();
        LocalDate newDate = this.paidThroughDate.plusMonths(paidCycles);

        if (newDate.isAfter(this.contractEndDate)) {
            throw new DomainRuleException("El pago excede la vigencia legal del contrato.");
        }
        
        return newDate;
    }

    public void updatePaidThroughDate(LocalDate newDate) {
        this.paidThroughDate = newDate;
    }

    public LocalDate getPaidThroughDate() { return this.paidThroughDate; }
}
```

### B. El Agregado: `Community` (Capa de Dominio)
Evalúa las reglas operativas y define su propio estado basado en los cálculos del Perfil Financiero.

```java
public class Community {
    private CommunityId id;
    private CommunityStatus status; // ACTIVE, SUSPENDED
    private FinancialProfile financialProfile; 

    public PaymentResult applyManualPayment(BigDecimal amount, LocalDate today) {
        // 1. Delegar matemáticas al Perfil Financiero
        LocalDate newDate = this.financialProfile.calculateNewPaidThroughDate(amount);
        this.financialProfile.updatePaidThroughDate(newDate);

        // 2. Transición de Estado Operativo (Plumas Vehiculares)
        boolean wasSuspended = (this.status == CommunityStatus.SUSPENDED);
        boolean isReactivated = false;

        if (newDate.isAfter(today)) {
            this.status = CommunityStatus.ACTIVE;
            isReactivated = wasSuspended; 
        } else {
            this.status = CommunityStatus.SUSPENDED; 
        }

        return new PaymentResult(newDate, this.status, isReactivated);
    }
}
```

### C. El Caso de Uso Orquestador (Capa de Aplicación)
Clase ligera protegida por `@Transactional`. No contiene lógica matemática, solo orquesta la infraestructura y el dominio.

```java
@Service
public class RegisterManualPaymentService implements RegisterManualPaymentUseCase {

    private final CommunityRepository communityRepository;
    private final PaymentRecordRepository paymentRepository;
    private final ApplicationEventPublisher eventPublisher;

    @Override
    @Transactional // Garantiza el cumplimiento de la RN Unit of Work (ACID)
    public String execute(RegisterManualPaymentCommand command) {
        
        // 1. Validación de Infraestructura
        if (paymentRepository.existsByReferenceNumber(command.referenceNumber())) {
            throw new ConflictException("El recibo SPEI ya fue registrado previamente.");
        }

        // 2. Hidratación del Dominio
        Community community = communityRepository.findById(command.communityId())
            .orElseThrow(() -> new NotFoundException("Fraccionamiento no encontrado"));

        // 3. Ejecución del Core de Negocio
        PaymentResult result = community.applyManualPayment(command.amount(), LocalDate.now());

        // 4. Persistencia Atómica
        communityRepository.save(community);
        paymentRepository.save(new PaymentRecord(command));

        // 5. Emisión de Eventos
        eventPublisher.publishEvent(new SubscriptionPeriodExtendedEvent(community.getId(), result.newDate()));
        
        if (result.isReactivated()) {
            eventPublisher.publishEvent(new SubscriptionReactivatedEvent(community.getId()));
        }

        return "Pago registrado exitosamente. Estado: " + result.newStatus();
    }
}
```
