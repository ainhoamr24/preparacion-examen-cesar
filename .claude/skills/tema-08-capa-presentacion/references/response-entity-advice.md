<!-- Referencia del Tema 08. Uso de ResponseEntity con los cuatro códigos habituales, records de respuesta (Summary y Detail) y de entrada (Insert y Update con @JsonFormat), estructura del @RestControllerAdvice con ErrorMessage, clase PaginatedResponse con @RequestParam, y gestión de idioma con ThreadLocal, interceptor y LocaleConfig. -->
# Referencia: ResponseEntity, @RestControllerAdvice, Paginación e Idioma
## ResponseEntity
Permite controlar el código de estado, cabeceras y cuerpo de la respuesta.
```java
// GET — 200 OK
return new ResponseEntity<>(body, HttpStatus.OK);
// POST — 201 Created
return ResponseEntity.status(HttpStatus.CREATED).body(created);
// DELETE — 204 No Content
return ResponseEntity.noContent().build();
// 404 Not Found
return ResponseEntity.status(HttpStatus.NOT_FOUND).build();
```
## Modelos de respuesta (record Java)
```java
// Listado
public record BookSummaryResponse(
    String isbn, String titleEs, String titleEn,
    BigDecimal basePrice, BigDecimal discountPercentage, BigDecimal price, String cover
) {}
// Detalle
public record BookDetailResponse(
    String isbn, String titleEs, String titleEn,
    String synopsisEs, String synopsisEn,
    BigDecimal basePrice, BigDecimal discountPercentage, BigDecimal price,
    String cover, LocalDate publicationDate,
    PublisherSummaryResponse publisher, List<AuthorSummaryResponse> authors
) {}
// Resumen de autor (dentro del detalle de un libro)
public record AuthorSummaryResponse(String name, String slug) {}
```
## Modelos de entrada (record Java)
```java
public record BookInsertRequest(
    String isbn, String titleEs, String titleEn,
    String synopsisEs, String synopsisEn, String cover,
    @JsonFormat(pattern = "dd-MM-yyyy") LocalDate publicationDate,
    BigDecimal basePrice, BigDecimal discountPercentage,
    Long publisherId, Long[] authorIds
) {}
public record BookUpdateRequest(
    Long id,
    // ... mismos campos que Insert
) {}
```
**`@JsonFormat`:** Jackson 2.x no serializa `LocalDate` por defecto. Solución: añadir `jackson-datatype-jsr310` + `@JsonFormat(pattern = "dd-MM-yyyy")`.
## @RestControllerAdvice
```java
public class ErrorMessage {
    private final String error;
    private final String message;
    public ErrorMessage(Exception exception) {
        this.error = exception.getClass().getSimpleName();
        this.message = exception.getMessage();
    }
    // MUST tener getters — Jackson los necesita para serializar a JSON
    public String getError() { return error; }
    public String getMessage() { return message; }
}
@RestControllerAdvice
public class ApiExceptionHandler {
    @ResponseStatus(HttpStatus.NOT_FOUND)
    @ExceptionHandler(ResourceNotFoundException.class)
    public ErrorMessage handleNotFound(ResourceNotFoundException ex) {
        return new ErrorMessage(ex);
    }
    @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
    @ExceptionHandler(Exception.class)
    public ErrorMessage handleGeneral(Exception ex) {
        return new ErrorMessage(ex); // mensaje genérico, no expone detalles internos
    }
}
```
## Paginación
```java
public class PaginatedResponse<T> {
    private List<T> data;
    private int total;
    private int currentPage;
    private int pageSize;
    private String next;     // null si es la última página
    private String previous; // null si es la primera página
}
```
**`@RequestParam` en el controlador:**
```java
@GetMapping
public ResponseEntity<PaginatedResponse<BookSummaryResponse>> getAll(
        @RequestParam(defaultValue = "1") int page,
        @RequestParam(required = false) Integer size) {
    int pageSize = (size != null) ? size : defaultPageSize;
    // ...
}
```
## Gestión de idioma con Accept-Language
```java
// ThreadLocal — cada hilo tiene su propio valor
public class LanguageUtils {
    private static final ThreadLocal<Locale> currentLocale = new ThreadLocal<>();
    public static void setCurrentLocale(Locale locale) { currentLocale.set(locale); }
    public static String getCurrentLanguage() {
        Locale locale = currentLocale.get();
        return locale != null ? locale.getLanguage() : Locale.getDefault().getLanguage();
    }
}
// Interceptor
public class CustomLocaleChangeInterceptor extends LocaleChangeInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
        String lang = request.getHeader("Accept-Language");
        Locale locale = lang != null ? Locale.forLanguageTag(lang) : Locale.of(defaultLanguage);
        LanguageUtils.setCurrentLocale(locale);
        return super.preHandle(request, response, handler);
    }
}
// Configuración — registra el interceptor en Spring
@Configuration
public class LocaleConfig implements WebMvcConfigurer {
    @Value("${app.language.default}")
    private String defaultLanguage;
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new CustomLocaleChangeInterceptor(defaultLanguage));
    }
}
```
