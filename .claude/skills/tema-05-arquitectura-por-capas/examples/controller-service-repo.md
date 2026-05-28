<!-- Ejemplos del Tema 05. Código Java de cada capa: controlador REST con @RestController, servicio con interfaz e implementación, interfaz de repositorio en dominio (inversión de dependencias) e implementación en persistencia. -->
# Ejemplos: Código por Capa
## Controlador
```java
@RestController
@RequestMapping("/api/books")
public class BookController {
    private final BookService bookService;
    public BookController(BookService bookService) {
        this.bookService = bookService;
    }
    @GetMapping
    public List<Book> getAll() {
        return bookService.getAll();
    }
    @GetMapping("/{id}")
    public BookDto findById(@PathVariable Long id) {
        return bookService.findById(id).orElseThrow(() -> new ResourceNotFoundException("Book not found"));
    }
}
```
## Servicio de dominio
```java
public interface BookService {
    Page<BookDto> findAll(int page, int size);
    Optional<BookDto> findById(Long id);
}

public class BookServiceImpl implements BookService {
    private final BookRepository bookRepository;
    public BookServiceImpl(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }
    @Override
    public Page<BookDto> findAll(int page, int size) {
        return bookRepository.findAll(page, size);
    }
    @Override
    public Optional<BookDto> findById(Long id) {
        return bookRepository.findById(id);
    }
}
```
## Repositorio de dominio
```java
public interface BookRepository {
    Page<BookDto> findAll(int page, int size);
    Optional<BookDto> findById(Long id);
}
```
## Implementación en persistencia
```java
public class BookRepositoryImpl implements BookRepository {
    @Override
    public Page<BookDto> findAll(int page, int size) {
        // Adaptar datos JPA al contrato del dominio
    }
    @Override
    public Optional<BookDto> findById(Long id) {
        // Buscar libro por id
    }
}
```
