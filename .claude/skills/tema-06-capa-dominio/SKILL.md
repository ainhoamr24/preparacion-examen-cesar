<!-- Skill principal del Tema 06. Define el conocimiento sobre la capa de dominio: independencia de frameworks, modelos anémicos vs ricos, DTOs con record Java, mapeadores Singleton, excepciones de dominio (BusinessException, ResourceNotFoundException, ValidationException) y testing con JUnit 5 y Mockito. -->
---
name: tema-06-capa-dominio
description: Conocimiento del Tema 06 sobre la Capa de Dominio. Cubre la independencia del dominio (sin imports externos), la inversión de dependencias, los modelos anémicos vs ricos/enriquecidos, los DTOs (BookDto para presentación, BookEntity para persistencia usando record Java), los mapeadores con patrón Singleton, los servicios (interfaz + implementación con inyección por constructor), las interfaces de repositorio en dominio, las excepciones (BusinessException, ResourceNotFoundException, ValidationException), validaciones en DTOs y servicios, y el testing con JUnit 5 y Mockito. Cárgalo para cualquier pregunta o implementación sobre la capa de dominio.
---
# Tema 06 — Capa de Dominio
## Ficheros de este skill
| Fichero | Contenido |
|---------|-----------|
| `references/modelos-dtos-mappers.md` | Referencia de modelos anémicos vs ricos, DTOs con record, mapeadores Singleton, excepciones y validaciones |
| `examples/book-domain.md` | Ejemplo completo del dominio con Book, BookDto, BookEntity, BookMapper, BookService, BookRepository y tests |
---
## Principios del dominio
La capa de dominio es el **corazón de la aplicación**:
- Contiene reglas de negocio y lógica fundamental.
- **MUST** estar aislada de frameworks externos (Spring, JPA, etc.).
- En teoría, ningún import que no sea de la biblioteca estándar de Java.
---
## Dos tipos de modelos
| Tipo | Descripción | Ventaja | Desventaja |
|------|-------------|---------|------------|
| **Anémico** | Solo getters/setters, sin lógica | Comparte modelo entre capas, sin mapeos | Antipatrón — lógica dispersa en servicios |
| **Rico/Enriquecido** | Tiene lógica de negocio (campos calculados, validaciones internas) | Lógica encapsulada donde corresponde | Necesita DTOs para transportar datos |
**En este curso se usan modelos ricos** con dos conjuntos de DTOs:
- `BookDto` → comunicación con **presentación**.
- `BookEntity` → comunicación con **persistencia**.
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
- JUnit 5 + Mockito (sin Spring).
- Tests de modelos: verificar lógica de negocio (precio calculado, añadir autores…).
- Tests de mapeadores: verificar mapeos null, campos correctos.
- Tests de servicios: mockear repositorio con `@Mock` + `@InjectMocks`.
