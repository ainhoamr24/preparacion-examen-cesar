<!-- Example for Topic 08 aligned with the documented project structure and without invented types. -->
# Complete Example: REST Controller aligned with the reference
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
## Exception
```java
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}
```
## JSON error response
```json
{
  "message": "Book not found"
}
```
