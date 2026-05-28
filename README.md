# EXAMEN-CESAR

Spring Boot base project for practising backend architecture, REST and persistence for the exam.

## Reference used

The documentation in this repository takes the following as its structural example:

- [isma-MR/RepasoExamenCesar](https://github.com/isma-MR/RepasoExamenCesar)

That repository is not copied literally; it is used to avoid describing an invented structure.

## Reference structure

The reference organises the main code around these packages:

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

## Architectural idea

- `controller`: exposes REST endpoints and delegates.
- `domain/model`: represents the domain.
- `domain/repository`: defines domain contracts.
- `domain/service`: declares use cases.
- `domain/service/impl`: implements business logic.
- `domain/service/dto`: moves data between layers.
- `mapper`: transforms between persistence entities and DTOs/models.
- `persistence/dao`: low-level JPA access.
- `persistence/repository`: adapts persistence to the contracts in `domain/repository`.
- `config` and `exception`: shared configuration and exceptions.

## Current status

This repository is still in its base phase. The documentation has been corrected so that future implementations follow a structure consistent with the reference.

## Commands

```bash
./mvnw test
./mvnw spring-boot:run
```
