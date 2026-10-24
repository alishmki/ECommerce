# Business Scenarios and Acceptance Criteria

## Scenario 1 — Successful order

1. Client submits an order.
2. Order Service stores `Pending` order state and an `OrderCreated` outbox record in one SQL transaction.
3. Outbox publisher publishes the event and handles publisher confirms.
4. Inventory reserves stock and publishes a result.
5. Orchestrator requests simulated payment after successful reservation.
6. Payment Service processes the request idempotently and publishes its result.
7. Orchestrator confirms the order and initiates shipping and notification.

Acceptance:
- Order eventually reaches `Confirmed`.
- Stock is reserved once.
- A repeated message does not cause a second charge or reservation.
- Every step can be traced using `CorrelationId`.

## Scenario 2 — Insufficient inventory

1. Inventory rejects the reservation.
2. It publishes `inventory.reservation-rejected`.
3. Orchestrator rejects or cancels the order.
4. Payment is not requested unless a prior payment exists; if it does, the defined compensation is executed.

Acceptance: stock never becomes negative and the order reaches a terminal or explicitly recoverable state.

## Scenario 3 — Temporary payment failure

1. Payment handler encounters a transient error.
2. Message is retried with a bounded delay.
3. A later attempt succeeds, or attempts are exhausted.
4. Exhausted messages enter the dead-letter path and raise an operational signal.

Acceptance: no hot loop, attempt count is observable, and dead-lettered messages can be diagnosed.

## Scenario 4 — Duplicate delivery

A worker commits its database transaction and stops before ACK. The broker may redeliver.

Acceptance: inbox/idempotency constraints and business keys prevent duplicate side effects; repeated handling returns or recognizes the prior result.

## Scenario 5 — Broker unavailable during publication

Order and outbox record commit successfully while RabbitMQ is unavailable. The publisher retries after recovery. A confirm may be lost after broker acceptance, so duplicate publication is possible.

Acceptance: the outbox record is not silently discarded; publication is retried and consumers tolerate duplicates.

## Scenario 6 — Invalid message

A malformed payload or unsupported schema is detected as a permanent failure.

Acceptance: it is not retried forever; useful diagnostic metadata is retained and the message reaches a controlled dead-letter/parking path.

## Scenario 7 — Traffic burst

Many orders arrive in a short period. Queue depth rises while multiple workers process messages with bounded prefetch.

Acceptance: worker concurrency and queue growth are measurable; memory remains bounded and the system recovers after the burst.

## Scenario 8 — Payment succeeded but order confirmation failed

Payment result is published but the orchestrator is temporarily unavailable.

Acceptance: the result remains recoverable; after recovery, the workflow continues. If the order cannot continue, a durable compensation workflow (such as refund) is triggered according to policy.

## Scenario 9 — Consumer dies before ACK

Stop the consumer after delivery but before acknowledgement.

Acceptance: the unacknowledged message is redelivered after the channel/connection closes and the consumer's idempotency prevents duplicate business effects.

## Scenario 10 — Unroutable publication

Publish to an exchange with no matching binding.

Acceptance: mandatory returns or alternate-exchange routing make the condition observable; the publisher does not silently assume the message reached a queue.

## Scenario 11 — Notification outage

Notification processing fails after the order is confirmed.

Acceptance: notification retries independently and does not roll back a completed order.

## Scenario 12 — Inventory concurrency

Two orders attempt to reserve the last unit concurrently.

Acceptance: at most one reservation succeeds; database constraints/transactions protect against overselling.

## Scenario 13 — Controlled replay from dead letter

An operator inspects a dead-lettered message, fixes the root cause, and replays it using a documented procedure.

Acceptance: replay is audited and safe against duplicate side effects.

## Scenario 14 — Broker node failure lab

In a multi-node lab, stop a node hosting quorum-queue replicas.

Acceptance: document observed availability and recovery behavior; do not assume a single-node local setup provides high availability.

## Order state model (initial proposal)

`Pending` → `AwaitingInventory` → `AwaitingPayment` → `Confirmed` → `PreparingShipment` → `Shipped`

Failure/cancellation paths may include `Rejected`, `Cancelled`, `CompensationPending`, `Refunded`, and `FailedNeedsReview`.

The implementation must define legal transitions and avoid arbitrary status updates.
