<!-- Referencia del Tema 08. Uso de ResponseEntity y respuestas HTTP básicas sin tipos inventados. -->
# Referencia: ResponseEntity y respuestas HTTP
## ResponseEntity
Permite controlar el código de estado y el cuerpo de la respuesta.
```java
return ResponseEntity.ok(body);
return ResponseEntity.status(HttpStatus.CREATED).body(created);
return ResponseEntity.noContent().build();
return ResponseEntity.status(HttpStatus.NOT_FOUND).build();
```
## Validación en entrada
```java
@PostMapping
public ResponseEntity<BookDto> create(@RequestBody @Valid BookDto request) {
    return ResponseEntity.status(HttpStatus.CREATED).body(bookService.create(request));
}
```
## Error simple
```json
{
  "message": "Book not found"
}
```
