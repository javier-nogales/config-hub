# config-hub

## Vision

...

## Problem it solves

...

## Scope (v1)

1. An admin can create/edit flags and configs for each app and environment.
2. Any client app can ask "give me ALL (falgs+config" in unique call.
3. The client catch and use it in runtime.

## Minimun model (small and stable)

Concepts:

- appId (string) -> "notes-api", "media-api"
- env (string/enum) -> "dev", "prod"
- key (string) -> "feature.search", "limits.pageSize"
- type (FLAG/CONFIG)
- value (for flag: boolean; for config: string) ...for now
- version (int/long) for cache/ETag
- updatedAt (date)

## Table (as simple as possible)

- app
  - id
  - name
- entry
  - app_id
  - env
  - key
  - kind (FLAG/CONFIG)
  - value (string)
  - version
  - updated_at
  - UNICQUE(app_id, env, key)

## Endpints

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

### Cache

- Supports `ETag` in snapshot and `If-None_match` in the client.
- if any changes, returns `304 Not Modified`.

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

Goal: Make the system usable as son as possible.

- Create SpringBoot project
- Create `entry`table + JPA entity
- Implement:
  - PUT flag
  - GET snapshot (no ETag yet)
- Harcode one test client or curl script
- Verify:
  - Create flag
  - Retrieve snapshot
  - Use it in a dummy app

Focus:

- End-to-end flow working
- No overengineering

### Iteration 2 - Complete Admin CRUD

Goal: stabilize the model.

- Add:
  - PUT config
  - GET entries
  - DELETE entry
  - GET single entry
- Add validation rules
- Add basic error handling (ProblemDetails)
- Add simple login

Focus:

- Clean controller/service separation
- No business login in controllers

### Iteration 3 - Caching & Snapshot Versioning

Goal: Make it production-like

- Add global snapshot revision
- Implement ETag support
- Support `If-None-Match`
- Ensure version increments only when data changes

Focus:

- HTTP semantics
- Statelessness
- Correct REST Behavior

### Iteration 4 - Refactor Architecture

Goal: Prepare to evolution

- Introduce `ConfigurationEntryStore` interface
- Move JPA into infrastructure adapter
- Isolate domain from JPA annotations
- Introduce vasic unit tests for use cases

Focus:

- Dependency direction
- Decoupling
- Cleand boundaries
