---
name: actions-knowledge
description: Domain knowledge for the EXAMEN-CESAR project covering Spring Boot, REST design and package organisation inspired by the project reference. Covers controllers, domain services, DTOs, mappers, persistence and naming conventions. Load it whenever you implement or review any Java class in the project.
---
# actions-knowledge
Domain knowledge for EXAMEN-CESAR. All Java implementations **MUST** follow these conventions.
## Files in this skill
| File | Content |
|------|---------|
| `references/actions.md` | Complete reference for REST controllers: HTTP verbs, status codes, endpoint structure and correct/incorrect examples |
---
## Project structure
The valid organisation for this project is the one documented in the local repository:
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
### Responsibilities
| Package | Responsibility | MUST NOT |
|---------|---------------|----------|
| `controller` | Receive HTTP, validate, delegate and respond | Business logic, direct persistence access |
| `domain/model` | Represent the domain | Mix HTTP details |
| `domain/repository` | Define domain contracts | Depend on controllers |
| `domain/service` | Declare use cases | Mix HTTP details |
| `domain/service/impl` | Implement business logic | Access the controller |
| `domain/service/dto` | Transport data between layers | Contain persistence logic |
| `mapper` | Convert between DTOs, model and JPA | Implement business logic |
| `persistence/dao` | Low-level JPA access | Expose HTTP |
| `persistence/repository` | Adapt persistence to domain contracts | Add business rules |
| `config` and `exception` | Shared configuration and exceptions | Complex business logic |
---
## REST controllers
```java
@RestController
@RequestMapping("/api/<resources>")
public class <Resource>Controller {
    private final <Resource>Service <resource>Service;

    public <Resource>Controller(<Resource>Service <resource>Service) {
        this.<resource>Service = <resource>Service;
    }
}
```
**Controller rules:**
- **MUST** use constructor injection.
- **MUST** validate inputs when applicable.
- **MUST NOT** implement business logic.
- **MUST NOT** inject `persistence` classes directly.
- **MUST NOT** catch business exceptions in the controller.
---
## Domain services
```java
public interface <Resource>Service {
    List<<Resource>Dto> findAll();
    Optional<<Resource>Dto> findById(Long id);
    <Resource>Dto create(<Resource>Dto dto);
    <Resource>Dto update(<Resource>Dto dto);
    void deleteById(Long id);
}

public class <Resource>ServiceImpl implements <Resource>Service {
    private final <Resource>Repository repository;

    public <Resource>ServiceImpl(<Resource>Repository repository) {
        this.repository = repository;
    }
}
```
**Rules:**
- **MUST** use `domain/repository` contracts.
- **MUST NOT** receive HTTP objects.
- **MUST NOT** mix JPA details into the service interface.
---
## Persistence
```java
public interface <Resource>Repository {
    Optional<<Resource>Dto> findById(Long id);
    <Resource>Dto save(<Resource>Dto dto);
}

public class <Resource>RepositoryImpl implements <Resource>Repository {
    private final <Resource>JpaDao <resource>JpaDao;
}
```
**Rules:**
- Contracts go in `domain/repository`.
- Implementations go in `persistence/repository`.
- JPA entities go in `persistence/dao/jpa/entity`.
- JPA DAOs go in `persistence/dao/jpa/impl`.
---
## DTOs and mappers
```java
public record <Resource>Dto(Long id, String name) {}
```
- DTOs go in `domain/service/dto`.
- Mappers go in `mapper`.
- Do not invent intermediate types that do not exist in the project.
---
## Exceptions
```java
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}
```
---
## Naming conventions
| Element | Convention | Example |
|---------|-----------|---------|
| Classes | `PascalCase` | `BookService`, `UserDto` |
| Variables and methods | `camelCase` | `bookTitle`, `findById` |
| Constants | `UPPER_SNAKE_CASE` | `MAX_RETRIES` |
| Packages | `lowercase` | `com.examencesar.domain.service` |
| Endpoints (URL) | `kebab-case` | `/api/book-authors` |
---
## Anti-patterns — MUST NOT
- ❌ Field-level `@Autowired`.
- ❌ Business logic in the controller.
- ❌ Controller injecting `persistence` directly.
- ❌ Service receiving HTTP objects.
- ❌ Mixing domain contract and JPA detail in the same class.
- ❌ Types or packages not present in the project structure.
