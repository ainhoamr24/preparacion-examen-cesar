---
name: actions-knowledge
description: Conocimiento de dominio del proyecto EXAMEN-CESAR sobre Spring Boot, arquitectura por capas y diseño REST. Cubre la estructura de controladores @RestController, servicios (@Service), repositorios JpaRepository, DTOs, gestión global de excepciones con @ControllerAdvice y nomenclatura. Cárgalo siempre que implementes o revises cualquier clase Java del proyecto.
---
# actions-knowledge
Conocimiento de dominio de EXAMEN-CESAR. Toda implementación Java **MUST** seguir estas convenciones.
## Ficheros de este skill
| Fichero | Contenido |
|---------|-----------|
| `references/actions.md` | Referencia completa de controladores REST: verbos HTTP, códigos de estado, estructura de endpoints, ejemplos correctos e incorrectos |
---
## Arquitectura por capas
El proyecto sigue una arquitectura **estricta por capas**. Ninguna capa puede saltar a otra no adyacente.
```
┌──────────────────────────┐
│      presentation        │  Controllers REST (@RestController)
├──────────────────────────┤
│      application         │  Servicios de aplicación (@Service)
├──────────────────────────┤
│        domain            │  Entidades, lógica de negocio, interfaces de repositorio
├──────────────────────────┤
│      persistence         │  Repositorios JPA (@Repository, JpaRepository)
├──────────────────────────┤
│    infrastructure        │  Configuración, seguridad, adaptadores externos
└──────────────────────────┘
```
### Responsabilidades
| Capa | Responsabilidad | MUST NOT |
|------|-----------------|----------|
| `presentation` | Recibir HTTP, validar con `@Valid`, delegar al servicio, devolver respuesta | Lógica de negocio, acceso a repositorios |
| `application` | Orquestar casos de uso, aplicar reglas de negocio | Objetos HTTP, detalles de persistencia |
| `domain` | Entidades JPA, value objects, interfaces de repositorio | Depender de Spring o capas superiores |
| `persistence` | Implementar acceso a BD con JPA/Spring Data | Lógica de negocio |
| `infrastructure` | Config, seguridad, clientes externos, `@ControllerAdvice` | Lógica de negocio |
### Estructura de paquetes
```
com.examencesar/
├── presentation/
│   └── <Recurso>Controller.java
├── application/
│   ├── <Recurso>Service.java        ← interfaz
│   └── impl/
│       └── <Recurso>ServiceImpl.java
├── domain/
│   └── <Entidad>.java
├── persistence/
│   └── <Recurso>Repository.java     ← extiende JpaRepository
└── infrastructure/
    └── exception/
        └── GlobalExceptionHandler.java
```
---
## Controladores REST
```java
@RestController
@RequestMapping("/api/<recursos>")
public class <Recurso>Controller {
    private final <Recurso>Service <recurso>Service;
    public <Recurso>Controller(<Recurso>Service <recurso>Service) {
        this.<recurso>Service = <recurso>Service;
    }
    @GetMapping
    public List<<RecursoDto>> findAll() {
        return <recurso>Service.findAll();
    }
    @GetMapping("/{id}")
    public <RecursoDto> findById(@PathVariable Long id) {
        return <recurso>Service.findById(id);
    }
    @PostMapping
    public ResponseEntity<<RecursoDto>> create(@RequestBody @Valid Create<Recurso>Request request) {
        return ResponseEntity.status(HttpStatus.CREATED).body(<recurso>Service.create(request));
    }
    @PutMapping("/{id}")
    public <RecursoDto> update(@PathVariable Long id, @RequestBody @Valid Update<Recurso>Request request) {
        return <recurso>Service.update(id, request);
    }
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> delete(@PathVariable Long id) {
        <recurso>Service.delete(id);
        return ResponseEntity.noContent().build();
    }
}
```
**Reglas del controlador:**
- **MUST** inyección por constructor (no `@Autowired` en campo).
- **MUST** `@Valid` en parámetros de entrada.
- **MUST NOT** lógica de negocio — solo delegar al servicio.
- **MUST NOT** inyectar repositorios directamente.
- **MUST NOT** capturar excepciones de negocio — van al `@ControllerAdvice`.
---
## Servicios de aplicación
```java
// Interfaz
public interface <Recurso>Service {
    List<<RecursoDto>> findAll();
    <RecursoDto> findById(Long id);
    <RecursoDto> create(Create<Recurso>Request request);
    <RecursoDto> update(Long id, Update<Recurso>Request request);
    void delete(Long id);
}
// Implementación
@Service
@Transactional
public class <Recurso>ServiceImpl implements <Recurso>Service {
    private final <Recurso>Repository repository;
    public <Recurso>ServiceImpl(<Recurso>Repository repository) {
        this.repository = repository;
    }
}
```
**Reglas:**
- **MUST** `@Service` en la implementación.
- **MUST** `@Transactional` en la clase o en métodos que escriben en BD.
- **MUST NOT** recibir objetos HTTP (`HttpServletRequest`, etc.).
- **MUST NOT** retornar entidades JPA — siempre DTOs.
---
## Repositorios JPA
```java
@Repository
public interface <Recurso>Repository extends JpaRepository<<Entidad>, Long> {
    Optional<<Entidad>> findByEmail(String email);
}
```
---
## Gestión global de excepciones
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(ResourceNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
                .body(new ErrorResponse(ex.getMessage()));
    }
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidation(MethodArgumentNotValidException ex) {
        String message = ex.getBindingResult().getFieldErrors().stream()
                .map(FieldError::getDefaultMessage)
                .collect(Collectors.joining(", "));
        return ResponseEntity.status(HttpStatus.BAD_REQUEST)
                .body(new ErrorResponse(message));
    }
}
```
---
## DTOs con validación
```java
public record Create<Recurso>Request(
        @NotBlank(message = "El nombre es obligatorio") String nombre,
        @Email(message = "El email no es válido") String email
) {}
```
---
## Nomenclatura
| Elemento | Convención | Ejemplo |
|----------|------------|---------|
| Clases | `PascalCase` | `BookService`, `UserDto` |
| Variables y métodos | `camelCase` | `bookTitle`, `findById` |
| Constantes | `UPPER_SNAKE_CASE` | `MAX_RETRIES` |
| Paquetes | `lowercase` | `com.examencesar.domain` |
| Endpoints (URL) | `kebab-case` | `/api/book-authors` |
---
## Anti-patrones — MUST NOT
- ❌ `@Autowired` en campo (usar inyección por constructor).
- ❌ Lógica de negocio en el controlador.
- ❌ Controlador inyectando repositorio directamente.
- ❌ Servicio recibiendo objetos HTTP.
- ❌ Retornar entidades JPA desde endpoints.
- ❌ `@Transactional` en el controlador.
- ❌ `try/catch` en el controlador para excepciones de negocio.
