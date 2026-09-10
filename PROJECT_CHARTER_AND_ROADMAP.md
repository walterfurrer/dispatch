# Dispatch — Project Charter & Roadmap

## Purpose

Dispatch is a production-style last-mile delivery orchestration platform. A
customer creates a shipment, dispatchers assign an available driver, drivers
report their location and progress, and an operations team can monitor delivery
status.

This is first a learning project and second a portfolio project. The objective
is to understand and personally build the system well enough to explain and
defend its design in an interview. The target is a polished, portfolio-ready
project by January–February 2027.

## Product Boundaries

Dispatch is not a Roadie clone and does not need every feature of a delivery
marketplace. It is an operations and integration platform for a fictional
courier business.

The first version supports:

- Creating, viewing, updating, and cancelling shipments.
- Managing drivers and their availability.
- Assigning a driver to a shipment.
- Enforcing a shipment delivery lifecycle.
- Recording driver locations.
- An internal operations dashboard for active shipments and drivers.
- A documented, tested REST API.

The first version does not need customer payments, route optimization, a mobile
driver application, real-time maps, notifications, or an event broker.

## Technical Direction

| Area | Initial choice | Why it exists |
| --- | --- | --- |
| Backend | Go with `net/http` or Chi | Learn conventional HTTP service development without a heavy framework. |
| Database | PostgreSQL with migrations | Model business data, use SQL, constraints, indexes, and transactions. |
| Local environment | Docker Compose | Make the service and dependencies reproducible. |
| Frontend | React, TypeScript, TanStack ecosystem | Build an operations UI against the real API while using existing strengths. |
| API contract | OpenAPI, later Swagger UI or Mintlify | Treat the API as a product and make it usable by others. |
| Testing | Go unit/integration tests; later frontend tests | Build confidence in business rules and API behavior. |

PostGIS, Redis, Kafka/Redpanda, webhooks, observability, AWS, and Kubernetes
are deliberately excluded until a real product or operational need justifies
them.

## Architecture Principles

1. Prefer a clear modular monolith before introducing services.
2. Keep the API contract, database constraints, and business rules consistent.
3. Use transactions for state changes that must succeed or fail together.
4. Add infrastructure to solve a demonstrated problem, not to decorate a
   résumé.
5. Capture consequential decisions in short Architecture Decision Records
   (ADRs).
6. Build each feature with tests and documentation as it is introduced.
7. The author writes the implementation; AI supports learning, design, review,
   and debugging unless a full implementation is explicitly requested.

## Delivery Lifecycle

The MVP state machine is:

```text
CREATED → ASSIGNED → EN_ROUTE_TO_PICKUP → PICKED_UP → IN_TRANSIT → DELIVERED
```

Alternative terminal states:

```text
CANCELLED
DELIVERY_FAILED
RETURNED
```

Each transition must have explicit rules: which prior states allow it, who can
make it, what data is required, and which changes must be atomic. This is a
core learning artifact, not a detail to leave to the UI.

## Roadmap

### Phase 0 — Charter, environment, and Go foundations

**Target: September 2026**

Goals:

- Define the domain vocabulary, MVP, non-goals, and delivery lifecycle.
- Set up a Go module, formatter/linter, test command, Docker Compose, and
  PostgreSQL development database.
- Learn the Go fundamentals needed for this project: packages, structs,
  interfaces, errors, context, JSON, HTTP handlers, and tests.
- Establish the repository structure and documentation conventions.

Done when:

- A new contributor can start the local environment from the README.
- The application exposes a small health endpoint and has a passing automated
  test.
- The first ADR explains the initial modular-monolith approach.

### Phase 1 — Core domain and REST API

**Target: October 2026**

Goals:

- Design PostgreSQL tables and migrations for shipments, drivers, assignments,
  and location history.
- Implement shipment and driver CRUD endpoints with consistent validation and
  error responses.
- Learn database constraints, foreign keys, indexes, pagination, and
  transactions.
- Publish an initial OpenAPI contract alongside the implementation.

Done when:

- A client can create and retrieve shipments and drivers through the API.
- Invalid requests and missing resources yield deliberate, documented HTTP
  responses.
- Migrations can create a clean database from scratch.
- Integration tests cover the principal API flows against PostgreSQL.

### Phase 2 — Dispatch workflow and business correctness

**Target: November 2026**

Goals:

- Implement driver assignment and the shipment state machine.
- Decide and document cancellation, reassignment, and failed-delivery rules.
- Add optimistic concurrency or another explicit protection against conflicting
  state transitions when the problem is encountered.
- Add simple API authentication and role-based authorization for operations
  users.

Done when:

- Invalid transitions cannot be made through either the API or database-backed
  business layer.
- Assignment and status transitions are tested for happy paths and key failure
  cases.
- An authenticated operations user can complete the full delivery workflow.

### Phase 3 — Operations dashboard and API polish

**Target: late November–December 2026**

Goals:

- Build a focused React/TypeScript operations dashboard using TanStack tools
  where they are helpful.
- Display active shipments, their status, assigned driver, and recent driver
  locations.
- Add filtering, pagination, and actionable error/loading states.
- Make the OpenAPI documentation consumable through Swagger UI or Mintlify.

Done when:

- An operations user can run the main dispatch workflow without using a REST
  client.
- The frontend calls only the documented Go API.
- A developer can use the published API documentation to exercise the core
  workflow.

### Phase 4 — Geospatial and real-time foundation

**Target: December 2026**

Goals:

- Introduce PostGIS only after basic location recording works.
- Add nearby-available-driver queries and explain the spatial index used.
- Choose a modest real-time update mechanism for the dashboard, such as polling
  first and Server-Sent Events or WebSockets only if warranted.

Done when:

- Dispatchers can find available drivers within a specified distance of a
  pickup location.
- The relevant query has an appropriate spatial index and is explained in the
  database documentation.
- The UI reflects status/location updates in a clear, reliable way.

### Phase 5 — Events and reliable webhooks

**Target: January 2027**

Goals:

- Define domain events for important shipment transitions.
- Start with a transactional outbox or similarly reliable publication design.
- Introduce Kafka or Redpanda only when asynchronous consumers exist.
- Build customer webhooks with signed requests, retry/backoff, timeout handling,
  delivery logs, idempotency expectations, replay, and a dead-letter path.

Done when:

- A shipment lifecycle event can reach a registered test endpoint reliably.
- Failed deliveries are observable, retryable, and do not block the primary
  dispatch workflow.
- The design explicitly documents at-least-once delivery and the consumer
  idempotency requirement.

### Phase 6 — Production readiness and portfolio finish

**Target: January–February 2027**

Goals:

- Add structured logging, metrics, request correlation, and basic tracing.
- Add CI for formatting, tests, and build verification.
- Deploy a sensible first version on AWS using Docker-based infrastructure.
- Perform basic security review, failure testing, and load testing.
- Complete architecture, API, database, event-system, and operational
  documentation.

Done when:

- A public demo environment and reproducible local environment are available.
- The repository README presents the product, architecture, setup, demo flow,
  and tradeoffs clearly.
- The project includes ADRs and diagrams sufficient for a technical interview
  walkthrough.

## Post-portfolio extensions

These are optional learning tiers, not prerequisites for declaring Dispatch
finished:

1. Redis for targeted caching, rate limiting, or ephemeral coordination.
2. Kubernetes after operating the AWS deployment reveals needs it can address.
3. Multi-region and multi-cloud real-time synchronization, including replication,
   consistency tradeoffs, conflict resolution, and disaster recovery.

## Initial Documentation Set

Create these as their subjects become concrete:

```text
README.md
docs/
  architecture.md
  api-design.md
  database-design.md
  event-system.md
  failure-handling.md
  decisions/
    001-modular-monolith.md
```

## First Milestone: Start Here

Begin Phase 0 with a short domain-modeling session before scaffolding anything.
The first deliverable is a one-page domain note that defines:

- The users of Dispatch and their responsibilities.
- The MVP shipment fields.
- The MVP driver fields.
- The shipment states and allowed transitions.
- The first three API resources: shipments, drivers, and assignments.
- Explicit non-goals for the MVP.

Once that note is agreed, scaffold the Go service and Docker Compose
environment. That sequencing ensures that the initial code reflects a model we
understand rather than a template we happen to have generated.

## Current Status and Handoff

Keep this section short and update it whenever a milestone is completed or the
next action changes. Use `docs/` for detailed design decisions, debugging notes,
and implementation history.

**Current phase:** Phase 0 — Charter, environment, and Go foundations

**Completed:**

- Project charter and phased roadmap created.
- Codex/VS Code workflow established.
- Repository-level `AGENTS.md` created with the project’s learning and
  engineering expectations.

**Next action:**

- Initialize Git in the project directory.
- Write the one-page domain note covering users, shipment fields, driver
  fields, allowed transitions, first API resources, and MVP non-goals.

**Last updated:** 2026-09-09
