<!-- Main skill for Topic 05. Defines knowledge about layered architecture: the three layers (presentation, domain, persistence), their responsibilities, dependency inversion by moving repository interfaces to the domain, and absolute rules for inter-layer communication. -->
---
name: tema-05-arquitectura-por-capas
description: Knowledge for Topic 05 on Layered Architecture. Covers the layered pattern (presentation/controller, domain, persistence), the responsibilities of each layer, Spring Boot presentation annotations (@RestController, @RequestMapping, @GetMapping, etc.), the domain structure without external dependencies, dependency inversion (repository interfaces in the domain), and inter-layer communication rules. Load it for any question or implementation about the layered structure.
---
# Topic 05 — Layered Architecture
## Files in this skill
| File | Content |
|------|---------|
| `references/capas-responsabilidades.md` | Detailed table of responsibilities, what DOES and DOES NOT go in each layer |
| `examples/controller-service-repo.md` | Code examples for each layer with their Spring annotations |
---
## The three basic layers
```
┌──────────────────────────┐
│   controller (presentation) │  Manages HTTP: receives, delegates, responds
├──────────────────────────┤
│         domain           │  Business logic, models, services
├──────────────────────────┤
│       persistence        │  Data access (CRUD)
└──────────────────────────┘
```
Upper layers use lower ones. Lower layers **DO NOT** depend on upper ones.
---
## Dependency inversion
Without inversion: `controller → domain → persistence` (everything depends on persistence).
With inversion: **repository interfaces are moved to the domain**.
```
controller → domain ← persistence
```
Result: persistence depends on domain, not the other way around. The DB can be changed without touching the domain.
---
## Absolute rules — MUST NOT
- **MUST NOT** put business logic in the presentation layer.
- **MUST NOT** have the domain import Spring, JPA or other external dependencies.
- **MUST NOT** have persistence contain business logic.
- **MUST NOT** skip layers (controller accessing persistence directly).
