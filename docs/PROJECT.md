# config-hub

## Vision

A centralized configuration and feature-flag service that allows multiple applications to retrieve runtime configuration in a single call.

## Problem it solves

Applications need a simple and consistent way to manage:

- Feature flags
- Environment-specific configuration
- Runtime changes without redeploying

This project provides a minimal and stable configuration hub.

## Scope (v1)

- An admin can create/edit flags and configs for each app and environment.
- Any client app can ask: "give me all flags and configs" in a single call.
- The client caches and uses it at runtime.
- Small and stable model.
- No overengineering.

## Concepts

- appId (string) -> "notes-api", "media-api"
- env (string/enum) -> "dev", "prod"
- key (string) -> "feature.search", "limits.pageSize"
- kind (FLAG | CONFIG)
- value
  - flag: boolean
  - config: string (for now)
- version (int/long) -> for cache/ETag
- updatedAt (date)

## Table (as simple as possible)

- app
  - id
  - name
- entry
  - app_id
  - env
  - key
  - kind (FLAG | CONFIG)
  - value (string)
  - updated_at
  `UNIQUE(app_id, env, key)`

## Endpoints

- Upsert Flag

```text
PUT /admin/apps/{appId}/envs/{env}/flags/{key}
```

- Upsert Config

```text
PUT /admin/apps/{appId}/envs/{env}/configs/{key}
```

- List Entries

```text
GET /admin/apps/{appId}/envs/{env}/entries
```

- Delete entry (or separated in flags/config)

```text
DELETE /admin/apps/{appId}/envs/{env}/{kind}/{key}
```

- Full snapshot

```text
GET /config/apps/{appId}/envs/{env}/snapshot
```

- Get simple entry

```text
GET /admin/apps/{appId}/envs/{env}/{kind}/{key}
```

> Admin endpoints are for management. Client endpoints are read-only and optimized for runtime usage.

### Cache

- Snapshot supoorts `ETag`
- Client supports `If-None-Match`.
- If nothing changed -> return `304 Not Modified`.
- If changed -> return `200 ok` with the new snapshot and a new ETag

## Architecture (v1)

The project follows a classic Layered Architecture:

- Controller layer (REST API)
- Service layer (application use cases)
- Repository layer (persistence)
- Domain layer (core entities and value objects)
- DTOs for transport

Controllers never access repositories directly.
All business logic flows through the service layer.

The architecture is intentionally simple and will evolve
towards a more hexagonal style if needed.

## Iteration plan

### Iteration 1 - Vertical Slice (End-to-End minimal flow)

Goal: Make the system usable as soon as possible.

- Create Spring Boot project
- Create `entry` table + JPA entity
- Implement:
  - PUT flag
  - GET snapshot (no ETag yet)
- Hardcode one test client or curl script

Verify:

- Create flag
- Retrieve snapshot
- Use it in a dummy app

Focus:

- End-to-end flow working
- No overengineering

### Iteration 2 - Complete Admin CRUD

Goal: stabilize the model.

Add:

- PUT config
- GET entries
- DELETE entry
- GET single entry
- Validation rules
- Basic error handling (ProblemDetails)
- Simple login

Focus:

- Clean controller/service separation
- No business logic in controllers

### Iteration 3 - Caching & Snapshot Versioning

Goal: Make it production-like

Add:

- Global snapshot revision.
- ETag support.
- If-None-Match handling.
- Ensure version increments only when data changes

Focus:

- HTTP semantics
- Statelessness
- Correct REST Behavior

### Iteration 4 - Refactor Architecture

Goal: Prepare for evolution

- Introduce `ConfigurationEntryStore` interface
- Move JPA into infrastructure adapter
- Isolate domain from JPA annotations
- Introduce basic unit tests for use cases

Focus:

- Dependency direction
- Decoupling
- Clean boundaries

## Why not using an existing solution?

This project is primarily a learning exercise.

It is not intended to compete with full-featured platforms like LaunchDarkly or Unleash.

Those tools already provide advanced capabilities such as:

- Targeting and segmentation
- Scheduling
- Audit trails
- Role-based access control
- UI dashboards
- Multi-tenant and distributed setups

The goal of config-hub is different:

- To explore and understand the design of a minimal configuration service
- To practice architectural decisions step by step
- To keep the model small and controlled
- To potentially use it in a personal self-hosted environment if it proves useful

If future requirements grow beyond this scope, adopting or integrating with a specialized platform would make more sense.
