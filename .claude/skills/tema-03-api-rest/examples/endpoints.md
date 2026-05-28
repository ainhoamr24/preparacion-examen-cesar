<!-- Ejemplos del Tema 03. Endpoints canónicos para el recurso books con todos los verbos HTTP, ejemplos de paginación y filtros con query params, y lista de casos correctos e incorrectos de diseño REST. -->
# Ejemplos de Endpoints REST
## Endpoints canónicos para el recurso `books`
```
GET    /api/books              → Lista todos los libros              → 200 OK
GET    /api/books/12           → Obtiene el libro con id=12          → 200 OK
POST   /api/books              → Crea un nuevo libro                 → 201 Created
PUT    /api/books/12           → Reemplaza el libro con id=12        → 200 OK
PATCH  /api/books/12           → Modifica parcialmente               → 200 OK
DELETE /api/books/12           → Elimina el libro con id=12          → 204 No Content
GET    /api/books/12/authors   → Lista los autores del libro id=12   → 200 OK
```
## Paginación y filtros — siempre query params
```
GET /api/books?page=3
GET /api/books?page=2&size=10
GET /api/books?genre=fantasy&sort=title
```
## ✅ Correctos
- `GET /api/books` → `200 OK` con array JSON
- `POST /api/books` → `201 Created` con objeto creado en el cuerpo
- `DELETE /api/books/5` → `204 No Content` sin cuerpo
- `GET /api/books?page=3` → paginación mediante query params
## ❌ Incorrectos
- `GET /api/getBooks` — verbo en la URL
- `POST /api/book` — recurso en singular
- `POST /api/books` devolviendo `200` — debe ser `201`
- `DELETE /api/books/5` devolviendo `200` — debe ser `204`
- `/books` sin el prefijo `/api`
- Respuesta en XML cuando la API es JSON
- `/api/books/page/3` — el filtro va en query param, no en el path
