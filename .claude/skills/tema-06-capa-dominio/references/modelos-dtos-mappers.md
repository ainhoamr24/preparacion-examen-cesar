<!-- Reference for Topic 06. DTOs with Java record, singleton mappers and validations consistent with the actual project structure. -->
# Reference: Models, DTOs, Mappers and Exceptions
## DTOs with Java record (Java 17+)
`record` types are useful for DTOs. The reference shows a `BookDto` in `domain/service/dto/`.
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
## Mappers — Singleton pattern
The reference uses singleton mappers via `getInstance()`.
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
Usage in service or repository:
```java
return bookRepository.findByIsbn(isbn)
    .map(BookMapper.getInstance()::fromBookJpaEntityToBookDto)
    .orElseThrow(() -> new ResourceNotFoundException("Book not found: " + isbn));
```
## Validations
### In DTOs
```java
public record BookDto(
    @NotNull(message = "ISBN is required")
    @Pattern(regexp = "\\d{13}", message = "ISBN must have 13 digits")
    String isbn,
    @NotNull(message = "Base price cannot be null")
    @DecimalMin(value = "0.0", inclusive = true, message = "Base price must be greater than or equal to 0")
    BigDecimal price
) {}
```
### In the entity
```java
public void addAuthor(Author author) {
    if (this.authors.contains(author))
        throw new BusinessException("Author already exists in this book");
    this.authors.add(author);
}
```
### In the service
```java
public BookDto create(BookDto bookDto) {
    if (findByIsbn(bookDto.isbn()).isPresent())
        throw new BusinessException("Book with isbn " + bookDto.isbn() + " already exists");
    return bookRepository.save(bookDto);
}
```
## Validation differences
| Type | Where | What it validates | Exception |
|------|-------|------------------|-----------|
| Input data | DTO | Data format/type | `ValidationException` |
| Internal business logic | Entity | Object invariants | `BusinessException` |
| Cross-object logic | Service | Rules spanning entities or using DB | `BusinessException` / `ResourceNotFoundException` |
