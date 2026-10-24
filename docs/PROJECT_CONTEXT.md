# Project Context — CommerceFlow

## 1. Purpose

CommerceFlow is a practical learning and portfolio project that demonstrates RabbitMQ messaging in a realistic e-commerce workflow. It is developed incrementally with mentoring and explicit acceptance criteria.

## 2. Learner and environment

- Role/background: backend developer experienced with ASP.NET Core and C#.
- OS: Windows.
- Runtime/SDK target: .NET 10.
- Container environment: Docker Desktop installed.
- Message broker experience: conceptual familiarity, no hands-on implementation yet.
- Database planned: SQL Server.

Confirm the installed SDK, Docker Engine, and exact package/image versions during Phase 1; the target versions above are not evidence that the local environment has been verified.

## 3. Business scenario

A customer places an order. The system coordinates inventory reservation, a simulated payment, order confirmation, shipping preparation, and notifications. Services communicate asynchronously through RabbitMQ where appropriate.

The workflow must tolerate temporary service outages, duplicate deliveries, and messages that cannot be processed. Business state must remain recoverable and observable.

## 4. Planned services

- Store API: public-facing API boundary; may initially be combined with Order API.
- Order Service: order state and transactional outbox.
- Inventory Service: reserve/release inventory.
- Payment Service: simulated charge/refund with idempotency.
- Shipping Service: shipment preparation/status.
- Notification Service: email/SMS simulation or local sink.
- Order Orchestrator: coordinates the long-running order workflow (Saga/process manager).
- BuildingBlocks: shared technical abstractions and versioned contracts, kept intentionally small.

Services must not read or write another service's database tables directly. A shared SQL Server instance is acceptable for local development, but use separate databases per service when practical.

## 5. Technical principles

- Use asynchronous messaging for cross-service business events and commands when the scenario benefits from it.
- Distinguish commands (requests to perform work) from events (facts that have happened).
- Distinguish publisher confirms, consumer acknowledgements, and business success.
- Assume at-least-once delivery and design consumers to be idempotent.
- Use a transactional outbox when a database state change must reliably result in a message.
- Apply bounded retry with delay to transient errors; do not create hot requeue loops.
- Route invalid or exhausted messages to a dead-letter path and provide an inspection/replay procedure.
- Propagate correlation and causation identifiers.
- Never claim end-to-end exactly-once processing merely because broker acknowledgements are enabled.
- Keep credentials out of source control and restrict local broker ports to loopback where possible.

## 6. Scope boundaries

Included:
- RabbitMQ exchanges, queues, bindings, routing, acknowledgements, publisher confirms, persistence, prefetch, retries, dead lettering, TTL, priority, RPC, security, management, observability, quorum queues, clustering, and a guided introduction to streams.
- Integration patterns such as outbox, idempotent consumers, and Saga.
- Automated tests and failure experiments.

Excluded:
- Real payment processing or storage of payment card data.
- Production deployment guarantees or compliance claims.
- Introducing every advanced feature into the business path when a focused lab would teach it more safely.

## 7. Definition of project completion

The project is complete when the core order workflow runs locally; documented success and failure scenarios have automated or repeatable tests; key RabbitMQ concepts have a demonstrated lab; and setup, architecture, operations, and known limitations are documented.

See `ROADMAP.md`, `SCENARIOS.md`, and `PROGRESS.md`.
