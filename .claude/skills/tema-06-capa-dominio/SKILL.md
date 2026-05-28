<!-- Main skill for Topic 06. Defines knowledge about domain, DTOs, mappers and exceptions without introducing types not backed by the project. -->
---
name: tema-06-capa-dominio
description: Knowledge for Topic 06 on the domain layer. Covers framework independence, dependency inversion, models, DTOs in `domain/service/dto`, singleton mappers, services, domain repositories, exceptions and testing with JUnit 5 and Mockito. Load it for any question or implementation about the domain layer.
---
# Topic 06 — Domain Layer
## Files in this skill
| File | Content |
|------|---------|
| `references/modelos-dtos-mappers.md` | Reference for models, DTOs with record, singleton mappers, exceptions and validations |
| `examples/book-domain.md` | Complete domain example with Book, BookDto, BookMapper, BookService, BookRepository and tests |
---
## Domain principles
The domain layer is the heart of the application:
- Contains business rules and fundamental logic.
- **MUST** be isolated from external frameworks wherever the module design allows.
- Defines contracts that persistence then implements.
---
## Models and DTOs
- `domain/model/` contains the domain model.
- `domain/service/dto/` contains DTOs used between layers.
- Do not introduce invented types just for "persistence" if the project does not actually use them.
---
## Domain exceptions
```java
public class BusinessException extends RuntimeException {
    public BusinessException(String message) { super(message); }
}
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) { super(message); }
}
public class ValidationException extends RuntimeException {
    public ValidationException(String message) { super(message); }
}
```
---
## Domain testing
- JUnit 5 + Mockito.
- Service tests with `@Mock` and `@InjectMocks`.
- Mapper tests when the conversion has relevant logic.
