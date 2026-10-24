# Development Runbook

This runbook will be expanded as commands are verified on the target environment. Treat examples as development-only.

## Local RabbitMQ

The intended local endpoints are:
- AMQP: `localhost:5672`
- Management UI: `http://localhost:15672`

Do not expose the management UI or use development credentials on an internet-facing host.

## Basic Docker Compose operations

Run from the directory containing the compose file:

```powershell
docker compose up -d
docker compose ps
docker compose logs --tail 100 rabbitmq
docker compose logs -f rabbitmq
docker compose stop rabbitmq
docker compose start rabbitmq
```

To stop and remove the container/network while preserving named volumes:

```powershell
docker compose down
```

**Destructive:** the following removes named volumes, including persisted broker data:

```powershell
docker compose down -v
```

Do not run the destructive command unless you intentionally want to reset local broker state.

## Initial troubleshooting

- Container exits: inspect `docker compose logs rabbitmq`.
- UI unavailable: verify container status and port mapping.
- Login fails after changing environment credentials: RabbitMQ's default user creation is initialization behavior; changing environment values on an already initialized persistent volume may not update an existing user's password. Inspect broker users or intentionally reset local state.
- Port already in use: identify the process using the port or change the host-side port mapping.
- Producer cannot connect: verify hostname/port from the producer's network location; `localhost` inside a separate container refers to that container, not the host or RabbitMQ container.

## Message failure investigation

When implemented, record:
- MessageId and CorrelationId
- Exchange and routing key
- Queue and consumer
- Redelivery/retry count
- Exception category and timestamp
- Dead-letter reason and original routing metadata

Never put credentials, card data, or sensitive personal information in logs.
