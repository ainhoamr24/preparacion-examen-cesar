---
name: actions-reviewer
description: Code review criteria for Java/Spring Boot in the EXAMEN-CESAR project. Defines what is BLOCKING (violates layered architecture, wrong HTTP status code, business logic in controller, code that does not compile), IMPORTANT (violates REST or Spring Boot conventions, missing tests) and MINOR (minor quality improvement). Load it whenever you review project code.
---
# actions-reviewer
Code review criteria for EXAMEN-CESAR. Every problem found **MUST** be classified as BLOCKING, IMPORTANT or MINOR according to these rules.
---
## BLOCKING — Prevents approval
Problems that break functionality, architecture or produce code that does not compile.
### Architecture and structure
- A controller injects a `persistence` class directly.
- A service receives HTTP objects (`HttpServletRequest`, `Model`, `HttpServletResponse`).
- Business logic (`if/else` logic, calculations) implemented in the controller.
- A package structure different from the one adopted in the project is documented or implemented.
### REST design
- Verbs in the URL: `/getBooks`, `/createUser`, `/deleteItem`.
- Semantically incorrect status codes: `POST` returning `200` instead of `201`, `DELETE` returning `200` instead of `204`.
- Endpoint that does not start with `/api`.
- Response that is not JSON.
### Spring Boot
- Business exceptions caught in the controller.
- A service returns a JPA entity directly to the controller (not a DTO).
- Code that does not compile (missing import, method not found, incompatible type).
---
## IMPORTANT — Violates project conventions
Problems that violate conventions established in CLAUDE.md but do not break functionality immediately.
### Spring Boot
- Field-level `@Autowired` instead of constructor injection.
- `@Transactional` on the controller instead of the service.
- Missing `@Valid` on input parameters that carry validation annotations.
- The service has no separate interface from the implementation when the module convention requires it.
### Testing
- Missing service unit tests for the implemented functionality.
- Tests that depend on execution order.
- JUnit 5 (`@Test`, `@ExtendWith`) not used or AssertJ not used for assertions.
### Naming
- Classes not following `PascalCase`.
- Methods or variables not following `camelCase`.
- Endpoints in `camelCase` instead of `kebab-case`.
- Singular resource in the URL.
---
## MINOR — Quality improvement
Minor issues that do not violate critical conventions but improve quality.
- Unused imports.
- Unused private methods (dead code).
- Missing `@DisplayName` in tests.
- Using `assertEquals` instead of `assertThat(...).isEqualTo(...)` from AssertJ.
- Unnecessary or outdated comments.
- Mixing `when/thenReturn` and `given/willReturn` from Mockito in the same test.
---
## Full review checklist
Before issuing the verdict, verify each point:
**Architecture:**
- [ ] Does the controller only delegate to the service without business logic?
- [ ] Does the controller not inject `persistence` classes?
- [ ] Does the service not receive HTTP objects?
- [ ] Are JPA entities not exposed directly in the endpoint response?
**REST:**
- [ ] Do endpoints use plural nouns?
- [ ] Do endpoints start with `/api`?
- [ ] Does `POST` return `201`? Does `DELETE` return `204`? Do `GET`/`PUT`/`PATCH` return `200`?
- [ ] Are responses JSON?
**Spring Boot:**
- [ ] Constructor injection in all beans?
- [ ] `@Valid` on input parameters that need it?
- [ ] Business exceptions not caught in the controller?
- [ ] Services follow the interface/implementation convention of the module?
**Testing:**
- [ ] Are there service unit tests?
- [ ] Is JUnit 5 and AssertJ used?
- [ ] Do tests follow the Given/When/Then pattern?
**Code:**
- [ ] Does the code compile without errors?
- [ ] No dead imports or dead code?
