# Messaging Topology

This document describes the intended topology. Declare only the exchanges, queues, and bindings needed for the current phase; do not create all of them before their behavior is understood.

## 1. Naming conventions

- Exchanges: `commerce.<purpose>`
- Queues: `commerce.<service>.<purpose>`
- Routing keys: lowercase dot-separated nouns/events or command destinations.
- Message contracts: stable names with an explicit schema version.
- IDs: `MessageId`, `CorrelationId`, and `CausationId`.

## 2. Planned exchanges

| Name | Type | Intended use |
|---|---|---|
| `commerce.events` | Topic | Business events routed by event name |
| `commerce.commands` | Direct | Commands routed to a named service destination |
| `commerce.broadcast` | Fanout | Broadcast to each bound queue |
| `commerce.priority` | Direct | Isolated priority-message lab |
| `commerce.dead` | Direct | Dead-letter destination exchange |
| `commerce.retry` | Direct | Optional explicit retry-routing lab |
| `commerce.unroutable` | Fanout or appropriate alternate type | Alternate exchange for unroutable publications |

An exchange does not store messages. A queue stores messages; bindings define how exchanges route them.

## 3. Planned routing keys

### Events
- `order.created`
- `order.confirmed`
- `order.rejected`
- `order.cancelled`
- `inventory.reserved`
- `inventory.reservation-rejected`
- `payment.succeeded`
- `payment.failed`
- `payment.refunded`
- `shipping.prepared`
- `shipping.dispatched`

### Commands
- `inventory.reserve`
- `inventory.release`
- `payment.charge`
- `payment.refund`
- `shipping.prepare`
- `notification.send`

## 4. Planned queues and bindings

| Queue | Exchange | Binding key | Owner |
|---|---|---|---|
| `commerce.inventory.order-created` | `commerce.events` | `order.created` | Inventory |
| `commerce.order.inventory-results` | `commerce.events` | `inventory.*` | Orchestrator |
| `commerce.order.payment-results` | `commerce.events` | `payment.*` | Orchestrator |
| `commerce.shipping.order-confirmed` | `commerce.events` | `order.confirmed` | Shipping |
| `commerce.notification.events` | `commerce.events` | `order.*` and selected `payment.*`, `shipping.*` | Notification |
| `commerce.audit.all-events` | `commerce.broadcast` | Fanout binding | Audit lab |
| `commerce.inventory.dead` | `commerce.dead` | `inventory` | Operations |
| `commerce.payment.dead` | `commerce.dead` | `payment` | Operations |

These are design candidates. Confirm whether one event exchange or separate event/command exchanges are appropriate when implementing the first real workflow. Do not bind a queue to an exchange that is not declared.

## 5. Retry and dead-letter policy

- Retry only transient errors.
- Bound retry attempts and use delay/backoff.
- Do not immediately requeue forever.
- Keep retry metadata observable.
- Route permanent validation errors and exhausted retries to a dead-letter path.
- Provide an operator inspection and controlled replay process.
- Verify exact TTL/DLX behavior against the RabbitMQ version and queue type used.

## 6. Important distinctions

- Multiple consumers on one queue normally compete for messages.
- Multiple independent queues bound to the same exchange can each receive a copy.
- Publisher confirms and consumer acknowledgements solve different problems.
- An unroutable message can be returned with mandatory publishing or routed through an alternate exchange, depending on configuration.
