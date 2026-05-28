<!-- Ejemplo del Tema 08. Controlador REST completo con los cuatro métodos (GET lista paginada, GET por ISBN, POST con 201, DELETE con 204) usando ResponseEntity, handler de excepciones con @RestControllerAdvice para los tres casos (404, 400, 500) y ejemplo del JSON de error resultante. -->
# Ejemplo completo: Controlador con ResponseEntity y Paginación
```java
@RestController
@RequestMapping("/api/books")
public class BookController {
    @Value("${app.base.url}")
    private String baseUrl;
    @Value("${app.pageSize.default}")
    private String defaultPageSize;
    private final BookService bookService;
    public BookController(BookService bookService) {
        this.bookService = bookService;
    }
    // GET /api/books?page=1&size=10  →  200 OK
    @GetMapping
    public ResponseEntity<PaginatedResponse<BookSummaryResponse>> getAll(
            @RequestParam(defaultValue = "1") int page,
            @RequestParam(required = false) Integer size) {
        int pageSize = (size != null) ? size : Integer.parseInt(defaultPageSize);
        List<BookSummaryResponse> books = bookService
            .getAll(page - 1, pageSize)
            .stream()
            .map(BookMapper::toBookSummaryResponse)
            .toList();
        int total = bookService.count();
        PaginatedResponse<BookSummaryResponse> response =
            new PaginatedResponse<>(books, total, page, pageSize, baseUrl + "/api/books");
        return new ResponseEntity<>(response, HttpStatus.OK);
    }
    // GET /api/books/{isbn}  →  200 OK
    @GetMapping("/{isbn}")
    public ResponseEntity<BookDetailResponse> getByIsbn(@PathVariable String isbn) {
        BookDetailResponse detail = BookMapper.toBookDetailResponse(bookService.getByIsbn(isbn));
        return new ResponseEntity<>(detail, HttpStatus.OK);
    }
    // POST /api/books  →  201 Created
    @PostMapping
    public ResponseEntity<BookDetailResponse> create(@RequestBody @Valid BookInsertRequest request) {
        BookDto created = bookService.create(BookMapper.fromInsertRequestToBookDto(request));
        return ResponseEntity.status(HttpStatus.CREATED)
            .body(BookMapper.toBookDetailResponse(created));
    }
    // DELETE /api/books/{isbn}  →  204 No Content
    @DeleteMapping("/{isbn}")
    public ResponseEntity<Void> delete(@PathVariable String isbn) {
        bookService.delete(isbn);
        return ResponseEntity.noContent().build();
    }
}
```
## Handler de excepciones
```java
@RestControllerAdvice
public class ApiExceptionHandler {
    // ResourceNotFoundException → 404
    @ResponseStatus(HttpStatus.NOT_FOUND)
    @ExceptionHandler(ResourceNotFoundException.class)
    public ErrorMessage handleNotFound(ResourceNotFoundException ex) {
        return new ErrorMessage(ex);
    }
    // ValidationException → 400
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    @ExceptionHandler(ValidationException.class)
    public ErrorMessage handleValidation(ValidationException ex) {
        return new ErrorMessage(ex);
    }
    // Cualquier otra excepción → 500
    @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
    @ExceptionHandler(Exception.class)
    public ErrorMessage handleGeneral(Exception ex) {
        return new ErrorMessage(ex);
    }
}
```
## Respuesta JSON de error
```json
{
  "error": "ResourceNotFoundException",
  "message": "Book with isbn 9999999 not found"
}
```
