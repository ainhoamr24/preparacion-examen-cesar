<!-- Referencia del Tema 06. DTOs con record Java (BookDto para presentación, BookEntity para persistencia), mapeadores con patrón Singleton y flujo obligatorio BookEntity→Book→BookDto, tres tipos de validaciones (en DTO, en entidad, en servicio) y tabla comparativa de qué excepción lanzar en cada caso. -->
# Referencia: Modelos, DTOs, Mapeadores y Excepciones
## DTOs con record Java (Java 17+)
Los `record` son inmutables. El compilador genera constructor, getters, equals, hashCode y toString automáticamente.
```java
// DTO para presentación (incluye price calculado)
public record BookDto(
    Long id, String isbn, String titleEs, String titleEn,
    String synopsisEs, String synopsisEn,
    BigDecimal basePrice, double discountPercentage, BigDecimal price,
    String cover, LocalDate publicationDate,
    PublisherDto publisher, List<AuthorDto> authors
) {}
// DTO para persistencia (sin price — no viene de BD)
public record BookEntity(
    Long id, String isbn, String titleEs, String titleEn,
    String synopsisEs, String synopsisEn,
    BigDecimal basePrice, double discountPercentage,
    String cover, LocalDate publicationDate,
    PublisherEntity publisher, List<AuthorEntity> authors
) {}
```
## Mapeadores — patrón Singleton
Flujo obligatorio: `BookEntity → Book → BookDto` (no saltar Book, tiene campos calculados).
```java
public class BookMapper {
    private static BookMapper INSTANCE;
    private BookMapper() {}
    public static BookMapper getInstance() {
        if (INSTANCE == null) INSTANCE = new BookMapper();
        return INSTANCE;
    }
    public Book fromBookEntityToBook(BookEntity e) { ... }
    public BookEntity fromBookToBookEntity(Book b) { ... }
    public BookDto fromBookToBookDto(Book b) { ... }
    public Book fromBookDtoToBook(BookDto dto) { ... }
}
```
Uso en el servicio:
```java
return bookRepository.findByIsbn(isbn)
    .map(BookMapper.getInstance()::fromBookEntityToBook)
    .map(BookMapper.getInstance()::fromBookToBookDto)
    .orElseThrow(() -> new ResourceNotFoundException("Book not found: " + isbn));
```
## Validaciones
### En DTOs (datos de entrada — frontera del dominio)
```java
public record BookDto(...) {
    public BookDto(...) {
        if (isbn == null || isbn.isBlank())
            throw new ValidationException("ISBN es obligatorio");
        if (basePrice == null || basePrice.compareTo(BigDecimal.ZERO) <= 0)
            throw new ValidationException("El precio base debe ser positivo");
        if (discountPercentage < 0 || discountPercentage > 100)
            throw new ValidationException("El descuento debe estar entre 0 y 100");
        this.authors = List.copyOf(authors); // evitar aliasing
    }
}
```
### En la entidad (lógica de negocio — depende del estado)
```java
public void addAuthor(Author author) {
    if (this.authors.contains(author))
        throw new BusinessException("Author already exists in this book");
    this.authors.add(author);
}
```
### En el servicio (lógica entre objetos o con repositorio)
```java
public BookDto create(BookDto bookDto) {
    if (findByIsbn(bookDto.isbn()).isPresent())
        throw new BusinessException("Book with isbn " + bookDto.isbn() + " already exists");
    // ...
}
```
## Diferencias entre validaciones
| Tipo | Dónde | Qué valida | Excepción |
|------|-------|------------|-----------|
| Datos de entrada | DTO | Formato/tipo del dato (ISBN nulo, precio negativo) | `ValidationException` |
| Lógica de negocio interna | Entidad | Invariantes del objeto (autor duplicado) | `BusinessException` |
| Lógica entre objetos | Servicio | Reglas que cruzan entidades o usan BD | `BusinessException` / `ResourceNotFoundException` |
