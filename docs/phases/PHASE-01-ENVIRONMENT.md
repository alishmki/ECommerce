# Phase 1 — Environment and First Message

## Goal

Run RabbitMQ locally, verify the Management UI, then create a minimal C# producer and consumer that exchange one message. Work one step at a time and do not mark a step complete without observed evidence.

## Prerequisites

- Windows with Docker Desktop installed and running.
- .NET 10 SDK targeted; verify with `dotnet --info` and `dotnet --list-sdks`.
- PowerShell.
- A code editor.

## Step 1 — Verify the local environment

Run:

```powershell
dotnet --info
dotnet --list-sdks
docker version
docker compose version
```

Acceptance:
- A .NET 10 SDK is listed.
- Docker client and server information is available.
- Docker Compose responds with a version.

If any command fails, stop and diagnose it before continuing.

## Step 2 — Create local RabbitMQ Compose configuration

Create `deploy/docker-compose.yml` (or temporarily use the repository root if agreed). Use a pinned, supported RabbitMQ 4.x management image tag selected at implementation time. Map AMQP and management ports to loopback only for local use. Use a named volume for `/var/lib/rabbitmq`. Development credentials must never be reused in production.

Acceptance:
- Compose configuration parses.
- The selected image is pulled successfully.
- Ports and volume are as intended.

## Step 3 — Start and verify RabbitMQ

Run Compose up, inspect status and logs, open the Management UI, and sign in with the locally configured credentials.

Acceptance:
- Container remains running.
- Broker startup completes without fatal errors.
- Management UI login succeeds.
- The environment's actual image/version and successful commands are recorded in `PROGRESS.md`.

## Step 4 — Create the solution and projects

Create a solution plus a minimal console producer and consumer (or Worker Service consumer if it improves the teaching objective). Add the RabbitMQ .NET client package at a compatible stable version and record the exact package version.

Acceptance:
- Solution builds.
- Both applications start without configuration errors.

## Step 5 — Publish and consume a message

Use a simple durable queue for this first lab. Declare topology explicitly, publish a text payload, and consume it. At this stage, explain the behavior before adding confirms, manual ACK, retry, or more complex topology.

Acceptance:
- Consumer prints the exact payload.
- Queue/message behavior can be observed in the UI.
- Commands to reproduce are documented.

## Step 6 — Basic experiments

- Publish several messages.
- Stop the consumer, publish another message, then start the consumer.
- Observe queued message count and delivery behavior.
- Record what is observed and what is not guaranteed yet.

Acceptance:
- Observations match the declared queue and consumer configuration.
- The learner can explain producer, broker, queue, and consumer responsibilities.

## Completion checklist

- [ ] Environment versions verified
- [ ] RabbitMQ container starts
- [ ] Management UI login succeeds
- [ ] .NET solution builds
- [ ] Producer publishes
- [ ] Consumer receives
- [ ] Basic queue experiment completed
- [ ] `PROGRESS.md` updated with evidence

## Important boundaries

This phase is not yet a production reliability implementation. Publisher confirms, consumer acknowledgements, persistent delivery semantics, outbox, retry, and idempotency are studied explicitly in later phases.
