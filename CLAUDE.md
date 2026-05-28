# CLAUDE.md — EXAMEN-CESAR

EXAMEN-CESAR es una aplicación backend orientada al examen sobre Spring Boot, APIs REST, arquitectura software y desarrollo web en entorno servidor. Su propósito es profesional: cada decisión técnica debe poder justificarse como buena práctica.

---

## Objetivo del proyecto

Construir una aplicación backend completa que demuestre dominio práctico de:

- **Spring Boot** — controladores REST, servicios, repositorios, configuración y arranque.
- **APIs REST** — diseño de recursos, verbos HTTP, códigos de estado, contratos JSON.
- **Arquitectura software** — separación de responsabilidades, capas, cohesión y bajo acoplamiento.
- **Desarrollo Web en Entorno Servidor** — ciclo petición/respuesta, seguridad básica, persistencia.

---

## Tecnologías

- **Java 17**
- **Spring Boot 3.x** — arranque, autoconfiguración, web MVC
- **Spring Data JPA** — repositorios con `JpaRepository`
- **Jakarta Persistence (JPA)** — mapeo objeto-relacional
- **Maven** — gestión de dependencias y ciclo de vida (`./mvnw`)
- **JUnit 5 + Mockito + AssertJ** — testing
- **H2** (en memoria) — base de datos para tests

### Compilar y arrancar

```bash
./mvnw clean install       # compilar y ejecutar todos los tests
./mvnw spring-boot:run     # arrancar la aplicación
./mvnw test                # solo tests
```

---

## Principios generales

Estas reglas son de obligado cumplimiento en cualquier tarea de generación o modificación de código:

- **No inventar requisitos.** Si algo no está especificado, preguntar antes de implementar.
- **No modificar la arquitectura sin autorización explícita.** Los cambios estructurales deben consensuarse.
- **Preguntar si falta contexto.** Es preferible pausar que asumir y equivocarse.
- **Mantener separación de responsabilidades.** Cada clase, módulo y capa tiene un rol único.
- **Priorizar claridad y mantenibilidad** sobre ingenio o brevedad.
- **Evitar complejidad innecesaria.** La solución más simple que funcione correctamente es la correcta.
- **No generar código muerto.** No dejar imports sin usar, métodos sin llamar ni clases sin instanciar.
- **Todo el código debe compilar.** No entregar código con errores de compilación bajo ninguna circunstancia.

---

## Convenciones REST

### Diseño de recursos

- Los endpoints se nombran con **sustantivos**, nunca verbos.
- Los recursos se expresan en **plural**.
- La lógica de la operación la define el **verbo HTTP**, no la URL.
- Todas las respuestas son **JSON**.
- Los códigos de estado HTTP deben ser **semánticamente correctos**.
- **Todos los endpoints comienzan por `/api`.**

### Verbos y semántica

| Verbo    | Uso                               | Código de éxito  |
|----------|-----------------------------------|------------------|
| `GET`    | Obtener uno o varios recursos     | `200 OK`         |
| `POST`   | Crear un nuevo recurso            | `201 Created`    |
| `PUT`    | Reemplazar un recurso completo    | `200 OK`         |
| `PATCH`  | Modificar parcialmente un recurso | `200 OK`         |
| `DELETE` | Eliminar un recurso               | `204 No Content` |

### Ejemplos

```
GET     /api/books            → Lista todos los libros
GET     /api/books/12         → Obtiene el libro con id 12
POST    /api/books            → Crea un nuevo libro             → 201
PUT     /api/books/12         → Reemplaza el libro con id 12
PATCH   /api/books/12         → Modifica parcialmente el libro con id 12
DELETE  /api/books/12         → Elimina el libro con id 12      → 204
GET     /api/books/12/authors → Lista los autores del libro con id 12
```

### Anti-patrones — prohibido

- ❌ Verbos en la URL: `/getBooks`, `/createUser`, `/deleteItem`
- ❌ Recurso en singular: `/book`
- ❌ `POST` devolviendo `200` — debe ser `201`
- ❌ `DELETE` devolviendo `200` — debe ser `204`
- ❌ Endpoints sin prefijo `/api`
- ❌ Respuestas en formato distinto a JSON

---

## Arquitectura backend

El backend sigue una **arquitectura por capas** estricta. Ninguna capa puede acceder a una capa no adyacente.

```
┌──────────────────────────┐
│      presentation        │  Controllers REST (@RestController)
├──────────────────────────┤
│      application         │  Servicios (@Service) — lógica de negocio
├──────────────────────────┤
│        domain            │  Entidades, interfaces de repositorio — sin dependencias externas
├──────────────────────────┤
│      persistence         │  Repositorios JPA — implementan interfaces de domain
├──────────────────────────┤
│    infrastructure        │  Configuración, seguridad, @ControllerAdvice global
└──────────────────────────┘
```

### Responsabilidades por capa

| Capa             | Responsabilidad                                                                           | MUST NOT                                              |
|------------------|-------------------------------------------------------------------------------------------|-------------------------------------------------------|
| `presentation`   | Recibir HTTP, validar entrada con `@Valid`, delegar al servicio, devolver respuesta        | Lógica de negocio, acceso directo a repositorios      |
| `application`    | Orquestar casos de uso, aplicar reglas de negocio, devolver DTOs                          | Objetos HTTP (`HttpServletRequest`, etc.)             |
| `domain`         | Entidades, value objects, interfaces de repositorio                                        | Imports de Spring, JPA u otros frameworks             |
| `persistence`    | Implementar acceso a BD con JPA/Spring Data                                               | Lógica de negocio                                     |
| `infrastructure` | Configuración de beans, seguridad, `@RestControllerAdvice`, clientes HTTP externos        | Lógica de negocio                                     |

### Estructura de paquetes

```
com.examencesar/
├── presentation/
│   └── <Recurso>Controller.java
├── application/
│   ├── <Recurso>Service.java          ← interfaz
│   └── impl/
│       └── <Recurso>ServiceImpl.java
├── domain/
│   └── <Entidad>.java
├── persistence/
│   └── <Recurso>Repository.java       ← extiende JpaRepository
└── infrastructure/
    └── exception/
        ├── GlobalExceptionHandler.java
        └── ResourceNotFoundException.java
```

---

## Convenciones Spring Boot

### Controladores REST

```java
@RestController
@RequestMapping("/api/books")
public class BookController {

    private final BookService bookService;

    public BookController(BookService bookService) {   // inyección por constructor — OBLIGATORIO
        this.bookService = bookService;
    }

    @GetMapping
    public List<BookDto> findAll() {
        return bookService.findAll();
    }

    @GetMapping("/{id}")
    public BookDto findById(@PathVariable Long id) {
        return bookService.findById(id);
    }

    @PostMapping
    public ResponseEntity<BookDto> create(@RequestBody @Valid CreateBookRequest request) {
        return ResponseEntity.status(HttpStatus.CREATED).body(bookService.create(request));
    }

    @PutMapping("/{id}")
    public BookDto update(@PathVariable Long id, @RequestBody @Valid UpdateBookRequest request) {
        return bookService.update(id, request);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> delete(@PathVariable Long id) {
        bookService.delete(id);
        return ResponseEntity.noContent().build();
    }
}
```

### Servicios

```java
// Interfaz
public interface BookService {
    List<BookDto> findAll();
    BookDto findById(Long id);
    BookDto create(CreateBookRequest request);
    BookDto update(Long id, UpdateBookRequest request);
    void delete(Long id);
}

// Implementación
@Service
@Transactional
public class BookServiceImpl implements BookService {
    private final BookRepository repository;

    public BookServiceImpl(BookRepository repository) {
        this.repository = repository;
    }
    // ...
}
```

### Reglas

- **MUST** inyección por constructor — nunca `@Autowired` en campo.
- **MUST** `@Valid` en los parámetros de entrada del controlador.
- **MUST** `@Transactional` en el servicio, no en el controlador.
- **MUST NOT** lógica de negocio en controladores — solo delegan.
- **MUST NOT** el servicio devolver entidades JPA — siempre DTOs.
- **MUST NOT** el controlador capturar excepciones de negocio — van al `@RestControllerAdvice`.

---

## Capa de dominio

La capa de dominio es el **núcleo de la aplicación**:

- **MUST** estar completamente aislada de frameworks externos (Spring, JPA, etc.).
- Ningún import fuera de la biblioteca estándar de Java.
- Define interfaces de repositorio que la capa de persistencia implementa (inversión de dependencias).

### Excepciones de dominio

```java
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) { super(message); }
}
public class BusinessException extends RuntimeException {
    public BusinessException(String message) { super(message); }
}
public class ValidationException extends RuntimeException {
    public ValidationException(String message) { super(message); }
}
```

### Gestión global de excepciones

```java
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

public record ErrorResponse(String message, int status) {}
```

---

## Testing

Todo módulo implementado debe acompañarse de tests en tres niveles:

| Nivel            | Descripción                                                                                       | Herramienta            |
|------------------|---------------------------------------------------------------------------------------------------|------------------------|
| **Unitario**     | Prueba clases aisladas (servicios, lógica de dominio) mockeando dependencias                      | JUnit 5 + Mockito      |
| **Integración**  | Prueba la interacción entre capas (controlador + servicio + repositorio) con contexto de Spring   | `@SpringBootTest`      |
| **Persistencia** | Prueba el acceso a base de datos con BD en memoria                                                | `@DataJpaTest` + H2    |

- Nomenclatura: `<ClaseTesteada>Test` para unitarios, `<ClaseTesteada>IT` para integración.
- Usar **JUnit 5** y **AssertJ** (`assertThat`). No usar `assertEquals` de JUnit directamente.
- Patrón **Given / When / Then** en todos los tests.
- `@DisplayName` descriptivo en cada test.
- Los tests **no deben depender del orden de ejecución**.

```java
@ExtendWith(MockitoExtension.class)
class BookServiceTest {
    @Mock BookRepository repository;
    @InjectMocks BookServiceImpl service;

    @Test
    @DisplayName("findById lanza ResourceNotFoundException cuando el libro no existe")
    void findById_notFound_throwsException() {
        // Given
        given(repository.findById(99L)).willReturn(Optional.empty());
        // When / Then
        assertThatThrownBy(() -> service.findById(99L))
                .isInstanceOf(ResourceNotFoundException.class);
    }
}
```

---

## Nomenclatura

| Elemento           | Convención         | Ejemplo                        |
|--------------------|--------------------|--------------------------------|
| Clases             | `PascalCase`       | `BookService`, `UserDto`       |
| Variables/métodos  | `camelCase`        | `bookTitle`, `findById`        |
| Constantes         | `UPPER_SNAKE_CASE` | `MAX_RETRIES`, `API_BASE_URL`  |
| Paquetes           | `lowercase`        | `com.examencesar.domain`       |
| Endpoints (URL)    | `kebab-case`       | `/api/book-authors`            |

---

## Skills disponibles

Antes de implementar o revisar cualquier código, cargar el skill correspondiente al tema:

### Skills de dominio (implementación y revisión)

| Skill                  | Cuándo cargarlo                                                              |
|------------------------|------------------------------------------------------------------------------|
| `actions-knowledge`    | Siempre que implementes o revises cualquier clase Java del proyecto           |
| `actions-reviewer`     | Siempre que revises código — define criterios BLOCKING / IMPORTANT / MINOR   |
| `actions-steps`        | Siempre que implementes una nueva funcionalidad — define el orden de capas    |

### Skills de conocimiento temático

| Skill                          | Contenido                                                       |
|--------------------------------|-----------------------------------------------------------------|
| `tema-03-api-rest`             | HTTP, verbos REST, códigos de estado, diseño de endpoints       |
| `tema-04-arquitectura-software`| Tipos de arquitecturas, arquitectura limpia, principios         |
| `tema-05-arquitectura-por-capas` | Las tres capas, inversión de dependencias, anotaciones Spring |
| `tema-06-capa-dominio`         | Modelos ricos/anémicos, DTOs, mapeadores Singleton, excepciones |
| `tema-07-capa-persistencia`    | JPA, DAO genérico, EntityManager, JPQL, MapStruct, tests H2     |
| `tema-08-capa-presentacion`    | ResponseEntity, @RestControllerAdvice, paginación, idioma       |

Todos los skills están en `.claude/skills/`. Cargar el skill relevante es **obligatorio** antes de generar código.

---

## Agentes disponibles

| Agente              | Cuándo usarlo                                                                           |
|---------------------|-----------------------------------------------------------------------------------------|
| `code-implementer`  | Para ejecutar un plan de implementación paso a paso con verificación en cada paso        |
| `code-reviewer`     | Para revisar y corregir código en un bucle iterativo hasta que no haya problemas         |

---

## Restricciones

Las siguientes acciones están **prohibidas**:

- Añadir **librerías o dependencias** no justificadas explícitamente por un requisito.
- **Romper la arquitectura por capas**: un controlador no puede acceder directamente a un repositorio.
- **Mezclar responsabilidades**: lógica de negocio en controladores, acceso a datos en servicios, etc.
- **Eliminar capas** o fusionarlas para "simplificar".
- Usar `@Autowired` en campo — siempre inyección por constructor.
- Retornar entidades JPA directamente desde endpoints — siempre DTOs.
- Capturar excepciones de negocio en el controlador — usar `@RestControllerAdvice`.
- Generar código que no compila o con warnings ignorados deliberadamente.

---

## Objetivo final

El resultado esperado es una aplicación:

- **Limpia** — código legible, sin ruido, sin duplicidad.
- **Mantenible** — fácil de modificar sin romper otras partes.
- **Escalable** — con estructura que permita crecer sin reescribir.
- **Didáctica** — comprensible para alguien que está aprendiendo las tecnologías implicadas.
- **Alineada con buenas prácticas REST** — contratos predecibles, semántica correcta, respuestas coherentes.
- **Coherente con arquitectura moderna** — separación de capas, inyección de dependencias, testing integrado.
