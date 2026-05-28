<!-- Skill principal del Tema 06. Define el conocimiento sobre dominio, DTOs, mapeadores y excepciones sin introducir tipos no respaldados por el proyecto. -->
---
name: tema-06-capa-dominio
description: Conocimiento del Tema 06 sobre la capa de dominio. Cubre independencia de frameworks, inversión de dependencias, modelos, DTOs en `domain/service/dto`, mapeadores singleton, servicios, repositorios de dominio, excepciones y testing con JUnit 5 y Mockito. Cárgalo para cualquier pregunta o implementación sobre la capa de dominio.
---
# Tema 06 — Capa de Dominio
## Ficheros de este skill
| Fichero | Contenido |
|---------|-----------|
| `references/modelos-dtos-mappers.md` | Referencia de modelos, DTOs con record, mapeadores singleton, excepciones y validaciones |
| `examples/book-domain.md` | Ejemplo completo del dominio con Book, BookDto, BookMapper, BookService, BookRepository y tests |
---
## Principios del dominio
La capa de dominio es el corazón de la aplicación:
- Contiene reglas de negocio y lógica fundamental.
- **MUST** estar aislada de frameworks externos siempre que el diseño del módulo lo permita.
- Define contratos que luego implementa la persistencia.
---
## Modelos y DTOs
- `domain/model/` contiene el modelo del dominio.
- `domain/service/dto/` contiene DTOs usados entre capas.
- No introducir tipos inventados solo para “persistencia” si el proyecto no los usa realmente.
---
## Excepciones del dominio
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
## Testing del dominio
- JUnit 5 + Mockito.
- Tests de servicios con `@Mock` y `@InjectMocks`.
- Tests de mapeadores si la conversión tiene lógica relevante.
