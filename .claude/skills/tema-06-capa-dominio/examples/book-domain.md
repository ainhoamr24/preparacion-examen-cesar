<!-- Example for Topic 06. Complete domain implementation of Book: rich model with calculated price and addAuthor with validation, service with interface and implementation using the mapper, and unit tests with Mockito verifying the success case and ResourceNotFoundException. -->
# Complete Example: Book Domain
## Rich model
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
        this.price = calculateFinalPrice(); // calculated field
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
## Service
```java
// Interface
public interface BookService {
    List<BookDto> getAll(int page, int size);
    BookDto getByIsbn(String isbn);           // throws exception if not found
    Optional<BookDto> findByIsbn(String isbn); // returns Optional, does not throw
    BookDto create(BookDto bookDto);
}
// Implementation
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
## Service test with Mockito
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
