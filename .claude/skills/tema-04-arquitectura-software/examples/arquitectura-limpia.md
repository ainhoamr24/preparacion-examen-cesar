<!-- Example for Topic 04. Description of Robert C. Martin's clean architecture: principle of core independence from frameworks, UI and DB, benefits table and typical violations to avoid. -->
# Example: Clean Architecture (Robert C. Martin — Uncle Bob)
## Basic principle
The **core of the system** = pure business logic (entities + use cases).
**Technological details** (DB, frameworks, UI) are peripheral and must be able to change without affecting the core.
## Benefits
| Benefit | Description |
|---------|-------------|
| **Framework independence** | Does not depend on any specific framework; can evolve |
| **UI independence** | Interfaces can change without touching the core logic |
| **DB independence** | Logic is not tied to any specific database |
| **Ease of testing** | External details are decoupled → simpler unit tests |
## Typical violations
- The domain layer imports Spring classes (`@Service`, `@Autowired`) → **violates framework independence**.
- The domain service uses `EntityManager` directly → **violates DB independence**.
- The controller contains business rules (`if price < 0 throw...`) → **violates separation of concerns**.
