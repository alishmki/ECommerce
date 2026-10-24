# Architecture Decision Records

Record material technical decisions here. New decisions should include context, options, decision, consequences, and date.

## ADR-001 — Use RabbitMQ for asynchronous service communication

- Status: Accepted for this learning project.
- Context: The project is intended to learn message routing, delivery, retry, and operational behavior in a realistic workflow.
- Decision: Use RabbitMQ as the message broker for asynchronous commands/events.
- Consequences: Services must handle duplicate delivery, eventual consistency, and broker outages. HTTP remains appropriate for client-facing APIs and suitable synchronous queries.

## ADR-002 — Use .NET 10 and C#

- Status: Accepted.
- Context: The learner's backend stack is ASP.NET Core and C#.
- Decision: Target .NET 10 and use the RabbitMQ .NET client.
- Consequences: Verify installed SDK and select a compatible stable client package at implementation time.

## ADR-003 — Use Docker Compose for local infrastructure

- Status: Accepted for development.
- Context: Reproducible setup on Windows is needed.
- Decision: Run RabbitMQ locally using Docker Compose; add SQL Server and observability dependencies when needed.
- Consequences: A single local broker is not a high-availability configuration. Secrets and port exposure must be reviewed before sharing or deploying.

## ADR-004 — Assume at-least-once processing and make consumers idempotent

- Status: Accepted.
- Context: Messages can be redelivered, and publisher confirmation can be lost after the broker accepted a message.
- Decision: Design business handlers to tolerate duplicates using durable idempotency keys/inbox records and unique constraints.
- Consequences: Exactly-once business effects are achieved, where needed, through application-level coordination and idempotency—not a blanket broker guarantee.

## ADR-005 — Use transactional outbox for database changes that publish events

- Status: Accepted for the order workflow.
- Context: A database commit and broker publish cannot normally be made one atomic local transaction.
- Decision: Persist outgoing event intent in the same SQL transaction as the business change; publish and mark delivery separately.
- Consequences: Duplicate publication is possible; consumers must be idempotent. Outbox backlog and failures must be observable.

## ADR-006 — Simulate payments

- Status: Accepted.
- Context: The project is for learning and must not process real money or store card data.
- Decision: Implement deterministic simulated success/failure and idempotent refund behavior.
- Consequences: Payment-provider-specific security and reconciliation remain out of scope.
