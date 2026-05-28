<!-- Examples for Topic 05. Java code for each layer: REST controller with @RestController, service with interface and implementation, domain repository interface (dependency inversion) and persistence implementation. -->
# Examples: Code per Layer
## Presentation layer — Controller
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
## Domain layer — Service (interface + implementation)
```java
// Interface — in domain/service/
public interface BookService {
    List<Book> getAll(int page, int size);
    Book findByIsbn(String isbn);
}
// Implementation — in domain/service/impl/
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
## Domain layer — Repository interface (dependency inversion)
```java
// Interface in domain/repository/ — implementation goes in persistence
public interface BookRepository {
    List<BookEntity> findAll(int page, int size);
    Optional<BookEntity> findByIsbn(String isbn);
}
```
## Persistence layer — Repository implementation
```java
// Implements the domain interface
public class BookRepositoryImpl implements BookRepository {
    @Override
    public List<BookEntity> findAll(int page, int size) {
        // Connect to the data source and retrieve books
    }
    @Override
    public Optional<BookEntity> findByIsbn(String isbn) {
        // Find book by ISBN
    }
}
```
