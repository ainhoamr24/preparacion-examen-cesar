# CLAUDE.md — EXAMEN-CESAR

These instructions describe how to work in this repository using the reference structure.

## Goal

Build a didactic and maintainable Spring Boot backend to practise:

- REST APIs
- separation of concerns
- persistence with JPA
- service and persistence testing

## Expected stack

- Java 17
- Spring Boot 3.x
- Maven
- Spring Web
- Spring Data JPA
- Jakarta Validation
- JUnit 5 + Mockito + AssertJ

## Main rule

Do not describe or implement an architecture different from the one actually used in the project. If functional context is missing, stop and ask for it.

## Structure to follow

The reference does not use the `presentation/application/infrastructure` structure previously documented. The structure to use as a base is:

```text
com.examencesar/
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

## Responsibilities

- `controller`: receives HTTP, validates input and delegates to the service.
- `domain/model`: represents domain entities and objects.
- `domain/repository`: contracts the domain needs.
- `domain/service`: use-case interface.
- `domain/service/impl`: business logic implementation.
- `domain/service/dto`: input and output DTOs.
- `mapper`: conversions between JPA, DTOs and models.
- `persistence/dao`: JPA operations and low-level access.
- `persistence/repository`: implementation of `domain/repository` contracts.
- `config`: Spring configuration.
- `exception`: business and resource-not-found exceptions.

## Implementation rules

- Use constructor injection.
- Avoid field-level `@Autowired`.
- Do not put business logic in the controller.
- Do not access persistence directly from the controller.
- Do not return unmapped persistence entities when the use case requires a DTO.
- Keep mappings outside the controller.
- If validation is added, place it consistently with `domain/validation`.
- If JPA adapters are created, keep them separate from the domain contracts.

## REST

- Endpoints under `/api` when exposing a public API.
- Resources with nouns.
- `GET` returns `200`.
- `POST` returns `201` when creating a resource.
- `DELETE` returns `204` when deleting without a body.

## Testing

- Unit tests for models in `src/test/java/.../domain/`: no mocks, `@ParameterizedTest` + `@CsvSource` for multiple scenarios.
- Unit tests for mappers in `src/test/java/.../mapper/`: no mocks, Arrange/Act/Assert pattern, `assertAll` for all fields; test that mapping `null` throws `BusinessException`.
- Unit tests for services in `src/test/java/.../domain/service/impl/`: `@ExtendWith(MockitoExtension.class)`, `@Mock` on the domain repository, `@InjectMocks` on the service, `Mockito.verify()` to confirm interactions.
- DTO validation tests in `src/test/java/.../domain/service/dto/`: `@ParameterizedTest` + `@CsvSource`, verify that invalid data throws `ValidationException` via `DtoValidator.validate(dto)`.
- Persistence tests (JPA DAO) in `src/test/java/.../persistence/dao/jpa/impl/`: `@DataJpaTest` + `@ContextConfiguration(TestConfig.class)` + `@AutoConfigureTestDatabase(replace = NONE)`, H2 in-memory, `@PersistenceContext EntityManager` to set up data, `assertAll` to verify results.
- `TestConfig` in `src/test/java/.../persistence/`: `@Configuration` class with `@EnableJpaRepositories`, `@EntityScan` and a `@Bean` per JPA DAO.
- Flyway migrations in `src/main/resources/db/migration/` with the format `V1__init.sql`, `V2__insert_data.sql`, etc.; H2 configuration in `src/test/resources/application-test.properties`.

## What to avoid

- Inventing packages that do not exist in the chosen architectural line.
- Mixing the domain contract with JPA detail in the same class.
- Documenting as mandatory a layer the project does not use.
- Rewriting the architecture without explicit authorisation.
