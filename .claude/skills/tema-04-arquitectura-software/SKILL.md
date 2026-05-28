<!-- Main skill for Topic 04. Defines knowledge about software architecture: definition, key components, goals, principles (separation of concerns, low coupling, high cohesion) and types of architectures. Entry point for loading this topic. -->
---
name: tema-04-arquitectura-software
description: Knowledge for Topic 04 on Software Architecture. Covers the definition of architecture, its key components (components, connectors, relationships), its goals (complexity, scalability, maintainability, flexibility), key principles (separation of concerns, technology independence, modularity, low coupling and high cohesion), Robert C. Martin's clean architecture and types of architectures (monolithic, layered, microservices, hexagonal, onion, event-driven, serverless). Load it for any theoretical question about software architecture.
---
# Topic 04 — Software Architecture
## Files in this skill
| File | Content |
|------|---------|
| `references/tipos-arquitecturas.md` | Comparative table of all architecture types with advantages and disadvantages |
| `examples/arquitectura-limpia.md` | Detailed description of Uncle Bob's clean architecture and its benefits |
---
## Definition
**Software architecture** is the set of fundamental decisions about the structure and behaviour of a system:
- How components are organised.
- How they interact with each other.
- The constraints and guidelines that affect those interactions.
### Key components
| Element | Description |
|---------|-------------|
| **Components** | Functional pieces: modules, services, databases |
| **Connectors** | How they communicate: HTTP, API, procedure calls |
| **Relationships** | Data flows and dependencies between components |
---
## Goals of good architecture
- **Complexity management** — divides the system into smaller parts.
- **Scalability** — grows in size without losing efficiency.
- **Maintainability** — incorporates changes without breaking existing features.
- **Flexibility** — replaces components without affecting the rest.
---
## Key principles
| Principle | Description |
|-----------|-------------|
| **Separation of concerns** | Each component has a single responsibility. Reduces coupling. |
| **Technology independence** | The core does not depend on specific technologies (DB, frameworks). |
| **Modularity** | Independent modules: developed, tested and deployed separately. |
| **Low coupling** | Few dependencies between components. |
| **High cohesion** | Components organised around a specific responsibility. |
| **Scalability and performance** | Designed from the start to handle growth. |
**Used in this course:** clean layered architecture.
