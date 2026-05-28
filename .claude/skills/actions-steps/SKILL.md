---
name: actions-steps
description: Guía de implementación paso a paso para el proyecto EXAMEN-CESAR. Define el orden correcto de implementación de una funcionalidad (entidad → repositorio → servicio → controlador → tests), los pasos concretos de cada capa, cómo verificar que cada paso es correcto y qué comprobar antes de dar un paso por completado. Cárgalo siempre que implementes una nueva funcionalidad.
---
# actions-steps
Guía de implementación de EXAMEN-CESAR. Define el orden y los pasos concretos para implementar cualquier funcionalidad nueva.
---
## Orden obligatorio de implementación
Implementa siempre en este orden. **MUST NOT** saltarte ninguna capa ni implementarlas en orden diferente.
```
1. Entidad de dominio (domain/)
2. Repositorio JPA (persistence/)
3. DTO de entrada y salida (application/ o presentation/)
4. Servicio: interfaz + implementación (application/)
5. Controlador REST (presentation/)
6. Gestión de excepciones (@ControllerAdvice en infrastructure/)
7. Tests unitarios del servicio
8. Tests de integración del controlador (opcional)
```
---
## Paso 1 — Entidad de dominio
Crea la entidad JPA en `src/main/java/com/examencesar/domain/`:
```java
@Entity
@Table(name = "<tabla>")
public class <Entidad> {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    @Column(nullable = false)
    private String <campo>;
    // getters y setters o usa Lombok @Data
}
```
**Verifica:** la clase tiene `@Entity`, tiene un `@Id`, los campos obligatorios llevan `@Column(nullable = false)`.
---
## Paso 2 — Repositorio JPA
Crea la interfaz en `src/main/java/com/examencesar/persistence/`:
```java
@Repository
public interface <Recurso>Repository extends JpaRepository<<Entidad>, Long> {
    // finders adicionales si los necesitas
    Optional<<Entidad>> findBy<Campo>(String <campo>);
}
```
**Verifica:** extiende `JpaRepository`, los finders adicionales tienen nombre correcto (Spring Data los genera automáticamente).
---
## Paso 3 — DTOs
Crea los DTOs en `src/main/java/com/examencesar/application/` (o `presentation/`):
```java
// DTO de salida (respuesta al cliente)
public record <Recurso>Dto(Long id, String <campo>) {}
// DTO de entrada para crear
public record Create<Recurso>Request(
        @NotBlank(message = "El <campo> es obligatorio") String <campo>
) {}
// DTO de entrada para actualizar
public record Update<Recurso>Request(
        @NotBlank(message = "El <campo> es obligatorio") String <campo>
) {}
```
**Verifica:** los DTOs de entrada tienen anotaciones de Bean Validation, el DTO de salida no expone la entidad JPA directamente.
---
## Paso 4 — Servicio: interfaz e implementación
### Interfaz en `src/main/java/com/examencesar/application/`:
```java
public interface <Recurso>Service {
    List<<Recurso>Dto> findAll();
    <Recurso>Dto findById(Long id);
    <Recurso>Dto create(Create<Recurso>Request request);
    <Recurso>Dto update(Long id, Update<Recurso>Request request);
    void delete(Long id);
}
```
### Implementación en `src/main/java/com/examencesar/application/impl/`:
```java
@Service
@Transactional
public class <Recurso>ServiceImpl implements <Recurso>Service {
    private final <Recurso>Repository repository;
    public <Recurso>ServiceImpl(<Recurso>Repository repository) {
        this.repository = repository;
    }
    @Override
    public List<<Recurso>Dto> findAll() {
        return repository.findAll().stream()
                .map(this::toDto)
                .toList();
    }
    @Override
    public <Recurso>Dto findById(Long id) {
        return repository.findById(id)
                .map(this::toDto)
                .orElseThrow(() -> new ResourceNotFoundException(
                        "<Recurso> con id " + id + " no encontrado"));
    }
    @Override
    @Transactional
    public <Recurso>Dto create(Create<Recurso>Request request) {
        <Entidad> entity = new <Entidad>();
        entity.set<Campo>(request.<campo>());
        return toDto(repository.save(entity));
    }
    @Override
    @Transactional
    public <Recurso>Dto update(Long id, Update<Recurso>Request request) {
        <Entidad> entity = repository.findById(id)
                .orElseThrow(() -> new ResourceNotFoundException(
                        "<Recurso> con id " + id + " no encontrado"));
        entity.set<Campo>(request.<campo>());
        return toDto(repository.save(entity));
    }
    @Override
    @Transactional
    public void delete(Long id) {
        if (!repository.existsById(id)) {
            throw new ResourceNotFoundException("<Recurso> con id " + id + " no encontrado");
        }
        repository.deleteById(id);
    }
    private <Recurso>Dto toDto(<Entidad> entity) {
        return new <Recurso>Dto(entity.getId(), entity.get<Campo>());
    }
}
```
**Verifica:** tiene `@Service`, `@Transactional`, inyección por constructor, devuelve DTOs (no entidades), lanza `ResourceNotFoundException` cuando el recurso no existe.
---
## Paso 5 — Controlador REST
Crea en `src/main/java/com/examencesar/presentation/`:
```java
@RestController
@RequestMapping("/api/<recursos>")
public class <Recurso>Controller {
    private final <Recurso>Service <recurso>Service;
    public <Recurso>Controller(<Recurso>Service <recurso>Service) {
        this.<recurso>Service = <recurso>Service;
    }
    @GetMapping
    public List<<Recurso>Dto> findAll() {
        return <recurso>Service.findAll();
    }
    @GetMapping("/{id}")
    public <Recurso>Dto findById(@PathVariable Long id) {
        return <recurso>Service.findById(id);
    }
    @PostMapping
    public ResponseEntity<<Recurso>Dto> create(@RequestBody @Valid Create<Recurso>Request request) {
        return ResponseEntity.status(HttpStatus.CREATED).body(<recurso>Service.create(request));
    }
    @PutMapping("/{id}")
    public <Recurso>Dto update(@PathVariable Long id, @RequestBody @Valid Update<Recurso>Request request) {
        return <recurso>Service.update(id, request);
    }
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> delete(@PathVariable Long id) {
        <recurso>Service.delete(id);
        return ResponseEntity.noContent().build();
    }
}
```
**Verifica:** `@RestController`, `@RequestMapping("/api/<recursos>")`, `@Valid` en parámetros de entrada, `POST` devuelve `201`, `DELETE` devuelve `204`.
---
## Paso 6 — Gestión global de excepciones
Si no existe, crea en `src/main/java/com/examencesar/infrastructure/exception/`:
```java
// Excepción de dominio
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}
// Handler global
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(ResourceNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
                .body(new ErrorResponse(ex.getMessage(), 404));
    }
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidation(MethodArgumentNotValidException ex) {
        String message = ex.getBindingResult().getFieldErrors().stream()
                .map(FieldError::getDefaultMessage)
                .collect(Collectors.joining(", "));
        return ResponseEntity.status(HttpStatus.BAD_REQUEST)
                .body(new ErrorResponse(message, 400));
    }
}
// DTO de error
public record ErrorResponse(String message, int status) {}
```
---
## Paso 7 — Tests unitarios del servicio
Crea en `src/test/java/com/examencesar/application/`:
```java
@ExtendWith(MockitoExtension.class)
class <Recurso>ServiceTest {
    @Mock
    private <Recurso>Repository repository;
    @InjectMocks
    private <Recurso>ServiceImpl service;
    @Test
    @DisplayName("findById devuelve el DTO cuando el recurso existe")
    void findById_existingResource_returnsDto() {
        // Given
        <Entidad> entity = new <Entidad>();
        entity.setId(1L);
        entity.set<Campo>("<valor>");
        given(repository.findById(1L)).willReturn(Optional.of(entity));
        // When
        <Recurso>Dto result = service.findById(1L);
        // Then
        assertThat(result.id()).isEqualTo(1L);
        assertThat(result.<campo>()).isEqualTo("<valor>");
    }
    @Test
    @DisplayName("findById lanza ResourceNotFoundException cuando el recurso no existe")
    void findById_nonExistingResource_throwsException() {
        // Given
        given(repository.findById(99L)).willReturn(Optional.empty());
        // When / Then
        assertThatThrownBy(() -> service.findById(99L))
                .isInstanceOf(ResourceNotFoundException.class);
    }
}
```
**Verifica:** usa JUnit 5, Mockito con `given/willReturn`, AssertJ con `assertThat`, patrón Given/When/Then, `@DisplayName` descriptivo.
---
## Verificación final de cada paso
Antes de marcar un paso como completado, comprueba:
- [ ] ¿El código compila? (`./mvnw compile` sin errores o `./gradlew compileJava`)
- [ ] ¿No hay imports sin usar?
- [ ] ¿La clase está en el paquete correcto según su capa?
- [ ] ¿Los tests del paso pasan? (`./mvnw test -Dtest=<NombreTest>`)
