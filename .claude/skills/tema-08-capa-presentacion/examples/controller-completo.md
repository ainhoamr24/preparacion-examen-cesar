<!-- Ejemplo del Tema 08 alineado con la estructura documentada del proyecto y sin tipos inventados. -->
# Ejemplo completo: Controlador REST alineado con la referencia
```java
@RestController
@RequestMapping("/api/books")
public class BookController {
    private final BookService bookService;

    public BookController(BookService bookService) {
        this.bookService = bookService;
    }

    @GetMapping
    public ResponseEntity<Page<BookDto>> findAll(
            @RequestParam(defaultValue = "1") int page,
            @RequestParam(defaultValue = "10") int size) {
        return ResponseEntity.ok(bookService.findAll(page, size));
    }

    @GetMapping("/{id}")
    public ResponseEntity<BookDto> findById(@PathVariable Long id) {
        return ResponseEntity.ok(
                bookService.findById(id)
                        .orElseThrow(() -> new ResourceNotFoundException("Book not found"))
        );
    }

    @PostMapping
    public ResponseEntity<BookDto> create(@RequestBody @Valid BookDto request) {
        return ResponseEntity.status(HttpStatus.CREATED).body(bookService.create(request));
    }

    @DeleteMapping("/{isbn}")
    public ResponseEntity<Void> delete(@PathVariable String isbn) {
        bookService.deleteByIsbn(isbn);
        return ResponseEntity.noContent().build();
    }
}
```
## Excepción
```java
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}
```
## Respuesta JSON de error
```json
{
  "message": "Book not found"
}
```
