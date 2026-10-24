# Learning and Delivery Roadmap

The roadmap is sequential. A phase is complete only when its acceptance criteria have been demonstrated. Estimates are planning ranges, not deadlines.

| Phase | Focus | Estimated effort | Main deliverable |
|---|---|---:|---|
| 0 | Documentation baseline | 0.5–1 day | Reviewed repository docs and initial decisions |
| 1 | Local environment and first message | 1–2 days | RabbitMQ running; C# producer and consumer exchange a message |
| 2 | Exchanges and routing | 2–3 days | Direct, Topic, Fanout, bindings, routing and unroutable-message lab |
| 3 | Delivery reliability | 3–4 days | Manual ACK/NACK, confirms, persistence, prefetch, idempotent consumer |
| 4 | Retry and dead lettering | 2–3 days | Bounded delayed retry and dead-letter inspection/replay |
| 5 | Order and inventory workflow | 4–6 days | Order API, inventory worker, SQL persistence, outbox |
| 6 | Payment and Saga | 4–6 days | Simulated payment, orchestration, compensation and idempotency |
| 7 | Advanced RabbitMQ | 3–5 days | TTL, priority, RPC, security, quorum queues, cluster lab and streams overview |
| 8 | Observability and resilience | 3–5 days | Metrics/tracing, load tests, failure tests, runbook and portfolio polish |

## Phase 0 — Documentation baseline

Review project scope, architecture, messaging topology, business scenarios, decisions, and progress tracking. Resolve any disagreement before implementing infrastructure.

## Phase 1 — Local environment and first message

1. Verify .NET 10 SDK and Docker Desktop.
2. Create local RabbitMQ Compose configuration with Management UI.
3. Verify container health, logs, and UI login.
4. Create solution and minimal producer/consumer projects.
5. Publish and consume a text message using RabbitMQ.Client.
6. Inspect the queue and message behavior in the UI.
7. Record tested versions and commands.

Acceptance: local broker is reachable; a C# producer publishes a message; a C# consumer receives the expected payload; setup is reproducible from a clean checkout.

## Phase 2 — Exchanges and routing

Implement focused labs for Default/Direct, Topic, Fanout, and Headers exchanges. Learn binding keys and wildcards. Demonstrate multiple independent queues receiving a copy versus multiple consumers competing on one queue. Test unroutable messages and alternate exchanges.

## Phase 3 — Delivery reliability

Demonstrate manual consumer acknowledgements, negative acknowledgements, publisher confirms, mandatory publishing/returns, durable topology, persistent messages, prefetch, and consumer concurrency. Add idempotency to protect business effects from redelivery.

## Phase 4 — Retry and dead lettering

Classify transient versus permanent failures. Implement bounded retries with delay, retry count tracking, dead-letter routing, poison-message handling, and an operator runbook. Prefer broker-supported policies and current RabbitMQ documentation over hard-coded assumptions where appropriate.

## Phase 5 — Order and inventory

Create Order API and Inventory worker. Persist order state and an outbox record in one SQL transaction. Publish outbox records with confirms. Make inventory reservation idempotent and protect stock from concurrent overselling.

## Phase 6 — Payment and Saga

Add simulated payment and refund operations with stable idempotency keys. Coordinate inventory, payment, and order state through a Saga/process manager. Test failures before and after each externally visible side effect.

## Phase 7 — Advanced features

Use isolated labs for TTL, message priority, consumer priority, RPC/reply-to/correlation, virtual hosts and permissions, TLS concepts, quorum queues, clustering, and streams. Add a feature to the main workflow only when it has a clear use case.

## Phase 8 — Observability and resilience

Add structured logs, correlation/causation IDs, OpenTelemetry traces and metrics. Test broker/worker restarts, duplicate delivery, load, queue growth, and recovery. Document dashboards, alerts, and operational procedures.

## Definition of done for every phase

- Code and configuration are committed.
- The acceptance criteria have been run, not merely read.
- Relevant tests and commands are documented.
- `PROGRESS.md` reflects actual observed status.
- Follow-up risks and the next step are recorded.
