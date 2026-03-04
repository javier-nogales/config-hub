# AI Context – Config Hub

## Repository

<https://github.com/javier-nogales/config-hub>

---

## Project Purpose

Config Hub is a learning project designed to:

- Consolidate backend development fluency (Java + Spring Boot).
- Practice clean REST API design.
- Apply architecture principles incrementally.
- Gain confidence in implementing production-like backend services.

This is NOT intended to become a full feature-flag platform (e.g. LaunchDarkly, Unleash).

Scope must remain intentionally limited.

---

## Current Primary Goal

Improve technical confidence in:

- REST API implementation
- Persistence layer (JPA/Hibernate)
- Transactions and validation
- HTTP semantics (ETag, caching, conditional requests)
- Layered structure

Architecture should evolve gradually after implementation fluency is achieved.

---

## Secondary Goal

Use the project to practice:

- Architectural thinking
- Separation of concerns
- Refactoring toward cleaner boundaries
- Avoiding overengineering

---

## Constraints

- Limited weekly time available.
- The project must remain manageable.
- Avoid unnecessary complexity.
- Prefer clarity over sophistication.

---

## Non-Goals

- No microservices.
- No distributed architecture.
- No premature hexagonal purity.
- No excessive abstraction layers.
- No speculative scalability.

---

## Architectural Principles

1. Start simple.
2. Deliver vertical slices first.
3. Refactor later.
4. Avoid framework leakage into domain when refactoring phase begins.
5. Only introduce abstractions when justified.

---

## How ChatGPT Should Help

When assisting:

- Challenge architectural decisions if they seem overcomplicated.
- Detect premature abstraction.
- Suggest simplifications when possible.
- Help reason about trade-offs.
- Encourage iterative improvement.
- Avoid generating large amounts of boilerplate code unless explicitly requested.
- Prioritize clarity and understanding over cleverness.

---

## Definition of Success

This project is successful if:

- I can design and implement a moderately complex REST API confidently.
- I can explain architectural decisions clearly.
- I feel technically fluent with the backend stack.
- I can discuss HTTP caching and ETag properly in an interview.
- The system remains small, coherent, and understandable.

---

End of context.
