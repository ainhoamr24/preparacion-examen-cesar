<!-- Referencia del Tema 06. DTOs con record Java, mapeadores singleton y validaciones coherentes con la estructura real del proyecto. -->
# Referencia: Modelos, DTOs, Mapeadores y Excepciones
## DTOs con record Java (Java 17+)
Los `record` son útiles para DTOs. En la referencia aparece un `BookDto` en `domain/service/dto/`.
```java
public record BookDto(
    Long id,
    String isbn,
    String titleEs,
    BigDecimal price,
    PublisherDto publisher,
    List<AuthorDto> authors
) {}
```
## Mapeadores — patrón Singleton
La referencia usa mapeadores singleton mediante `getInstance()`.
```java
public class BookMapper {
    private static BookMapper INSTANCE;
    private BookMapper() {}

    public static BookMapper getInstance() {
        if (INSTANCE == null) INSTANCE = new BookMapper();
        return INSTANCE;
    }

    public BookDto fromBookJpaEntityToBookDto(BookJpaEntity e) { ... }
    public BookDto fromBookToBookDto(Book b) { ... }
    public Book fromBookDtoToBook(BookDto dto) { ... }
    public BookJpaEntity fromBookDtoToBookJpaEntity(BookDto dto) { ... }
}
```
Uso en servicio o repositorio:
```java
return bookRepository.findByIsbn(isbn)
    .map(BookMapper.getInstance()::fromBookJpaEntityToBookDto)
    .orElseThrow(() -> new ResourceNotFoundException("Book not found: " + isbn));
```
## Validaciones
### En DTOs
```java
public record BookDto(
    @NotNull(message = "ISBN es obligatorio")
    @Pattern(regexp = "\\d{13}", message = "ISBN debe tener 13 dígitos")
    String isbn,
    @NotNull(message = "El precio base no puede ser nulo")
    @DecimalMin(value = "0.0", inclusive = true, message = "El precio base debe ser mayor o igual a 0")
    BigDecimal price
) {}
```
### En la entidad
```java
public void addAuthor(Author author) {
    if (this.authors.contains(author))
        throw new BusinessException("Author already exists in this book");
    this.authors.add(author);
}
```
### En el servicio
```java
public BookDto create(BookDto bookDto) {
    if (findByIsbn(bookDto.isbn()).isPresent())
        throw new BusinessException("Book with isbn " + bookDto.isbn() + " already exists");
    return bookRepository.save(bookDto);
}
```
## Diferencias entre validaciones
| Tipo | Dónde | Qué valida | Excepción |
|------|-------|------------|-----------|
| Datos de entrada | DTO | Formato/tipo del dato | `ValidationException` |
| Lógica de negocio interna | Entidad | Invariantes del objeto | `BusinessException` |
| Lógica entre objetos | Servicio | Reglas que cruzan entidades o usan BD | `BusinessException` / `ResourceNotFoundException` |
