# AGENTS.md — EXAMEN-CESAR

## Working principle

Before generating code or documentation:

- review the actual local repository structure
- compare it against the reference
- do not invent packages, layers or flows

## Reference architecture

The reference is organised as follows:

```text
src/main/java/com/.../
├── config/
├── controller/
├── domain/
│   ├── model/
│   ├── repository/
│   ├── service/
│   │   ├── dto/
│   │   └── impl/
│   └── validation/
├── exception/
├── mapper/
└── persistence/
    ├── dao/
    │   └── jpa/
    │       ├── entity/
    │       └── impl/
    └── repository/
```

## Correct reading of that structure

- `controller` exposes endpoints.
- `domain` concentrates the model, contracts and services.
- `persistence` implements data access and adapts JPA.
- `mapper` centralises conversions.
- `exception` groups reusable exceptions.
- `config` holds Spring configuration.

## Instructions for agents

- Do not describe the architecture as `presentation/application/infrastructure` if the project does not use it.
- When implementing a new feature, follow the flow `controller -> domain/service -> domain/repository -> persistence`.
- If a DTO is needed, place it in `domain/service/dto` unless the local repository adopts a different explicit convention.
- If a service implementation is needed, place it in `domain/service/impl`.
- If JPA adaptation is needed, separate `dao/jpa/entity`, `dao/jpa/impl` and `persistence/repository`.
- If a reference class is empty or incomplete, use it only as a location hint, not as a quality model.

## Minimum conventions

- Constructor injection.
- Controllers without business logic.
- Business exceptions outside the controller.
- Mappings outside the controller.
- Code must always compile.

## Testing

Follow the folder example from the reference repository:

- `src/test/java/.../domain/service/impl/` for service unit tests
- `src/test/java/.../persistence/repository/` for persistence tests
- `src/test/resources/` for test configuration and data

## Restriction

If the local documentation contradicts the structure observed in the reference repository, it must be corrected before generating more code.
