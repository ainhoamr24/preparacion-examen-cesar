<!-- Ejemplo del Tema 06. Implementación completa del dominio Book: modelo rico con precio calculado y addAuthor con validación, servicio con interfaz e implementación usando el mapper, y tests unitarios con Mockito verificando caso de éxito y ResourceNotFoundException. -->
# Ejemplo completo: Dominio de Book
## Modelo rico
```java
public class Book {
    private Long id;
    private String isbn;
    private BigDecimal basePrice;
    private double discountPercentage;
    private BigDecimal price;
    private List<Author> authors;
    public Book(Long id, String isbn, BigDecimal basePrice,
                double discountPercentage, ..., List<Author> authors) {
        this.basePrice = basePrice;
        this.discountPercentage = discountPercentage;
        this.price = calculateFinalPrice(); // campo calculado
        this.authors = authors != null ? new ArrayList<>(authors) : new ArrayList<>();
    }
    public BigDecimal calculateFinalPrice() {
        BigDecimal discount = basePrice
            .multiply(BigDecimal.valueOf(discountPercentage))
            .divide(BigDecimal.valueOf(100), 2, RoundingMode.HALF_UP);
        return basePrice.subtract(discount).setScale(2, RoundingMode.HALF_UP);
    }
    public void addAuthor(Author author) {
        if (this.authors.contains(author))
            throw new BusinessException("Author already exists");
        this.authors.add(author);
    }
}
```
## Servicio
```java
// Interfaz
public interface BookService {
    List<BookDto> getAll(int page, int size);
    BookDto getByIsbn(String isbn);           // lanza excepción si no existe
    Optional<BookDto> findByIsbn(String isbn); // devuelve Optional, no lanza
    BookDto create(BookDto bookDto);
}
// Implementación
public class BookServiceImpl implements BookService {
    private final BookRepository bookRepository;
    public BookServiceImpl(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }
    @Override
    public BookDto getByIsbn(String isbn) {
        return bookRepository.findByIsbn(isbn)
            .orElseThrow(() -> new ResourceNotFoundException("Book not found: " + isbn));
    }
}
```
## Test del servicio con Mockito
```java
@ExtendWith(MockitoExtension.class)
class BookServiceImplTest {
    @Mock private BookRepository bookRepository;
    @InjectMocks private BookServiceImpl bookServiceImpl;
    @Test
    @DisplayName("getAll should return list of BookDto")
    void getAll_ShouldReturnListOfBooks() {
        // Arrange
        when(bookRepository.findAll(0, 10)).thenReturn(List.of(/* BookDto... */));
        // Act
        List<BookDto> result = bookServiceImpl.getAll(0, 10);
        // Assert
        assertAll(
            () -> assertNotNull(result),
            () -> assertEquals(2, result.size())
        );
        Mockito.verify(bookRepository).findAll(0, 10);
    }
    @Test
    @DisplayName("getByIsbn throws ResourceNotFoundException when book not found")
    void getByIsbn_NotFound_ThrowsException() {
        when(bookRepository.findByIsbn("999")).thenReturn(Optional.empty());
        assertThrows(ResourceNotFoundException.class, () -> bookServiceImpl.getByIsbn("999"));
    }
}
```
