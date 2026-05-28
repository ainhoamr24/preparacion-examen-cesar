<!-- Reference for Topic 08. ResponseEntity usage and basic HTTP responses without invented types. -->
# Reference: ResponseEntity and HTTP responses
## ResponseEntity
Allows controlling the status code and response body.
```java
return ResponseEntity.ok(body);
return ResponseEntity.status(HttpStatus.CREATED).body(created);
return ResponseEntity.noContent().build();
return ResponseEntity.status(HttpStatus.NOT_FOUND).build();
```
## Input validation
```java
@PostMapping
public ResponseEntity<BookDto> create(@RequestBody @Valid BookDto request) {
    return ResponseEntity.status(HttpStatus.CREATED).body(bookService.create(request));
}
```
## Simple error
```json
{
  "message": "Book not found"
}
```
