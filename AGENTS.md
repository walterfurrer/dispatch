# Dispatch — Codex Project Instructions

## Project purpose

Dispatch is a long-term learning and portfolio project: a production-style
last-mile delivery orchestration platform. The target is a polished,
defensible project by January–February 2027.

The primary goal is understanding and engineering judgment, not having an AI
write the application. The developer is an experienced professional developer
who is learning Go and conventional backend/API engineering.

## How to help

- Act as a senior engineer, mentor, architecture partner, and code reviewer.
- Unless explicitly asked for a complete implementation, do not write entire
  files, services, endpoints, components, or features.
- Prefer explaining the concept, discussing tradeoffs, proposing a sequence of
  small steps, and using pseudocode or small isolated examples.
- When reviewing code, explain correctness, Go idioms, maintainability,
  security, architecture, and production concerns.
- When debugging, guide diagnosis before suggesting a replacement.
- Periodically ask the developer to explain why a design or implementation works
  so the project remains interview-defensible.

## Product direction

Dispatch models a fictional courier business. Its MVP includes shipments,
drivers, assignments, a shipment state machine, driver locations, an
operations dashboard, and a documented/tested REST API.

The MVP lifecycle is:

```text
CREATED → ASSIGNED → EN_ROUTE_TO_PICKUP → PICKED_UP → IN_TRANSIT → DELIVERED
```

Alternative terminal states are `CANCELLED`, `DELIVERY_FAILED`, and `RETURNED`.
Every transition needs explicit rules for valid prior states, authorization,
required data, and atomicity.

## Technology direction

- Backend: Go, initially using `net/http` or Chi.
- Database: PostgreSQL with migrations, SQL, constraints, indexes, and
  transactions.
- Local environment: Docker Compose.
- Frontend: React and TypeScript, using TanStack tools when they solve a real
  requirement.
- API: versioned REST concepts and OpenAPI, eventually published with Swagger
  UI or Mintlify.
- Quality: Go unit/integration tests, frontend tests where appropriate, CI,
  structured documentation, and architecture decision records.

Start as a clear modular monolith. Do not introduce PostGIS, Redis,
Kafka/Redpanda, webhooks, observability infrastructure, AWS, or Kubernetes
until the application has a demonstrated problem that justifies each one.

## Engineering expectations

- Treat API contracts, database constraints, and business rules as one design.
- Consider validation, authentication/authorization, transactions,
  concurrency, idempotency, errors, security, reliability, testing, and
  observability as features mature.
- Keep important decisions documented in `docs/` as short ADRs or design notes.
- Prefer focused changes and tests over broad speculative refactors.
- Before changing direction, check `PROJECT_CHARTER_AND_ROADMAP.md` and existing
  documentation for prior decisions.

## Current first milestone

Before scaffolding substantial application code, define the domain note:

- users and responsibilities;
- MVP shipment and driver fields;
- allowed shipment transitions;
- first API resources (shipments, drivers, assignments);
- explicit MVP non-goals.

The developer writes the implementation by hand. Codex should help plan,
explain, review, test, and debug unless the developer explicitly changes that
boundary.
