# Architecture

## 1. Style

CommerceFlow uses event-driven communication for asynchronous cross-service workflows. It is not necessary to split every component into a separate deployable process immediately: start with small focused labs, then evolve them into services as the workflow grows.

## 2. Logical context

```text
Client
  |
  v
Store / Order API ---- SQL database (Orders + Outbox)
                              |
                              v
                       Outbox Publisher
                              |
                              v
                        RabbitMQ Broker
                       /       |       \
                      v        v        v
                Inventory   Payment   Notification
                      \        |        /
                       v       v       v
                       Order Orchestrator
                              |
                              v
                    Shipping / Order updates
```

This is a logical diagram, not a fixed final topology. Exact event routes and orchestration ownership are specified in `MESSAGING_TOPOLOGY.md` and `SCENARIOS.md`.

## 3. Service responsibilities

### Order Service
Accepts an order request, validates basic input, stores order state, and writes an outbox event in the same database transaction. It does not directly change inventory or payment data.

### Inventory Service
Owns stock and reservation state. Reservations are idempotent and protected against concurrent overselling. It publishes reservation success or rejection.

### Payment Service
Uses a simulated payment provider. It accepts an idempotency key and supports a compensating refund operation. No real payment credentials are used.

### Order Orchestrator
Tracks the workflow state and decides which command follows each result. It records state transitions durably. A Saga is a sequence of local transactions with compensating actions, not a distributed SQL transaction.

### Shipping Service
Creates a simulated shipment only after the order reaches the appropriate state. It deduplicates requests.

### Notification Service
Consumes relevant events and records or simulates email/SMS delivery. Notification failure must not roll back an already completed order.

## 4. Messaging conventions

- Commands request an action and are routed to the service responsible for that action.
- Events report a fact and may be consumed by multiple independent services.
- Use durable queues for important workflows.
- Use persistent messages and publisher confirms for important publications.
- Consumers ACK only after durable business processing is complete.
- Consumers must tolerate duplicate deliveries.
- Use outbox for atomic database state plus outgoing-event intent.
- Use correlation and causation IDs for traceability.
- Keep event contracts backward-compatible where possible and version deliberate breaking changes.

## 5. Data ownership

Each service owns its own schema/database boundary. Local development may use one SQL Server instance with separate databases. No service accesses another service's tables directly.

## 6. Reliability boundaries

RabbitMQ confirms broker acceptance; they do not confirm consumer business processing. Consumer ACK confirms delivery handling to the broker; it does not create a distributed transaction with SQL Server. Outbox, idempotency, durable state, retries, and reconciliation close these gaps.

## 7. Deployment evolution

1. Single-node RabbitMQ for local learning.
2. Durable queues and failure experiments.
3. Multi-node quorum-queue lab.
4. Observability and operational controls.

A local Docker setup is not a high-availability deployment.
