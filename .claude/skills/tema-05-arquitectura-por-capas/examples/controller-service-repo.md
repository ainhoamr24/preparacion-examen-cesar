<!-- Ejemplos del Tema 05. Código Java de cada capa: controlador REST con @RestController, servicio con interfaz e implementación, interfaz de repositorio en dominio (inversión de dependencias) e implementación en persistencia. -->
# Ejemplos: Código por Capa
## Capa de presentación — Controlador
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
    @GetMapping("/{isbn}")
    public Book findByIsbn(@PathVariable String isbn) {
        return bookService.findByIsbn(isbn);
    }
}
```
## Capa de dominio — Servicio (interfaz + implementación)
```java
// Interfaz — en domain/service/
public interface BookService {
    List<Book> getAll(int page, int size);
    Book findByIsbn(String isbn);
}
// Implementación — en domain/service/impl/
public class BookServiceImpl implements BookService {
    private final BookRepository bookRepository;
    public BookServiceImpl(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }
    @Override
    public List<Book> getAll(int page, int size) {
        return bookRepository.findAll(page, size);
    }
    @Override
    public Book findByIsbn(String isbn) {
        return bookRepository.findByIsbn(isbn)
            .orElseThrow(() -> new ResourceNotFoundException("Book not found: " + isbn));
    }
}
```
## Capa de dominio — Interfaz de repositorio (inversión de dependencias)
```java
// Interfaz en domain/repository/ — la implementación va en persistencia
public interface BookRepository {
    List<BookEntity> findAll(int page, int size);
    Optional<BookEntity> findByIsbn(String isbn);
}
```
## Capa de persistencia — Implementación del repositorio
```java
// Implementa la interfaz de dominio
public class BookRepositoryImpl implements BookRepository {
    @Override
    public List<BookEntity> findAll(int page, int size) {
        // Conectar con el origen de datos y recuperar libros
    }
    @Override
    public Optional<BookEntity> findByIsbn(String isbn) {
        // Buscar libro por ISBN
    }
}
```
