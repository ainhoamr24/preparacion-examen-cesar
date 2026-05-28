<!-- Reference for Topic 04. Comparative table of 7 architecture types (monolithic, layered, microservices, hexagonal, onion, event-driven, serverless) with advantages and disadvantages, and their relationship with Uncle Bob's clean architecture. -->
# Reference: Types of Software Architectures
| Type | Description | Advantages | Disadvantages |
|------|-------------|-----------|--------------|
| **Monolithic** | The entire system as a single block | Simple at the start | Hard to maintain and scale as it grows |
| **Layered** | Layers with specific responsibilities (presentation, business, data) | Very popular, organised, clear | Rigid dependencies if not managed well |
| **Microservices** | Independent services that communicate with each other | Independent scalability, separate lifecycles | Distribution and communication complexity |
| **Hexagonal (Ports & Adapters)** | Business logic separated from the outside via ports and adapters | High testability, independence from the outside | Greater initial complexity |
| **Onion** | Concentric layers; the core is business logic | Decoupling, similar to clean | Steeper learning curve |
| **Event-driven** | Production, detection, consumption and reaction to events | Useful for distributed and scalable systems | Hard to debug and reason about |
| **Serverless** | Cloud provider manages scaling; dev only writes code | High availability, low operating cost | Vendor lock-in |
## Architectures derived from clean architecture
Clean architecture (Uncle Bob) gave rise to several architectures that share its principles:
- **Layered architecture** — the most used in this course.
- **Hexagonal architecture** — ports and adapters.
- **Onion architecture** — concentric layers.
