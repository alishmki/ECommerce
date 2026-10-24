# CommerceFlow

**An event-driven e-commerce learning project built with ASP.NET Core, C#, SQL Server, and RabbitMQ.**

CommerceFlow is a practical, incremental project for learning RabbitMQ through realistic business workflows rather than isolated messaging examples.

## Goals

- Learn RabbitMQ fundamentals and operational concepts hands-on.
- Build asynchronous workflows across independently deployable services.
- Practice reliable messaging, retries, dead-lettering, idempotency, and the transactional outbox pattern.
- Explore observability, security, performance, and failure recovery.
- Maintain clear architecture decisions, acceptance criteria, and progress records.

## Current status

**Phase 0 — Documentation and project baseline.** No infrastructure or application behavior is considered verified until it has been run and tested locally.

See:
- [Project context](docs/PROJECT_CONTEXT.md)
- [Roadmap](docs/ROADMAP.md)
- [Architecture](docs/ARCHITECTURE.md)
- [Messaging topology](docs/MESSAGING_TOPOLOGY.md)
- [Scenarios and acceptance criteria](docs/SCENARIOS.md)
- [Decisions](docs/DECISIONS.md)
- [Progress](docs/PROGRESS.md)
- [Runbook](docs/RUNBOOK.md)
- [Phase 1 guide](docs/phases/PHASE-01-ENVIRONMENT.md)

## Planned technology baseline

- C# / .NET 10
- ASP.NET Core Worker Services and Web API
- RabbitMQ 4.x, using the Management plugin in local development
- RabbitMQ.Client 7.x (confirm the current stable patch before implementation)
- SQL Server
- Docker Compose
- OpenTelemetry; metrics backend to be selected during the observability phase

Exact image tags, package versions, and environment-specific settings must be recorded when implemented and verified.

## Repository layout

```text
CommerceFlow/
├── docs/
│   ├── PROJECT_CONTEXT.md
│   ├── ROADMAP.md
│   ├── ARCHITECTURE.md
│   ├── MESSAGING_TOPOLOGY.md
│   ├── SCENARIOS.md
│   ├── DECISIONS.md
│   ├── PROGRESS.md
│   ├── RUNBOOK.md
│   └── phases/
│       └── PHASE-01-ENVIRONMENT.md
├── src/       # application code, added incrementally
├── tests/     # automated tests, added incrementally
└── deploy/    # local infrastructure, added incrementally
```

## Working agreement

1. Work on one explicitly scoped step at a time.
2. Explain the purpose and relevant concepts before implementation.
3. Run the step and inspect actual output before marking it complete.
4. Do not claim a feature works until a repeatable test demonstrates it.
5. Update `docs/PROGRESS.md` and relevant technical documents at each phase boundary.
6. Record material architectural changes in `docs/DECISIONS.md`.
7. Prefer a small working implementation over a large unverified code dump.

## Non-goals

This is a learning and portfolio project, not a production-ready commerce platform. Payment integrations are simulated; no real card data or real payments are processed.
