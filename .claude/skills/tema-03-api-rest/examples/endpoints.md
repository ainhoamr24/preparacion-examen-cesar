<!-- Examples for Topic 03. Canonical endpoints for the books resource with all HTTP verbs, pagination and filter examples with query params, and a list of correct and incorrect REST design cases. -->
# REST Endpoint Examples
## Canonical endpoints for the `books` resource
```
GET    /api/books              → List all books              → 200 OK
GET    /api/books/12           → Get book with id=12         → 200 OK
POST   /api/books              → Create a new book           → 201 Created
PUT    /api/books/12           → Replace book with id=12     → 200 OK
PATCH  /api/books/12           → Partially modify            → 200 OK
DELETE /api/books/12           → Delete book with id=12      → 204 No Content
GET    /api/books/12/authors   → List authors of book id=12  → 200 OK
```
## Pagination and filters — always query params
```
GET /api/books?page=3
GET /api/books?page=2&size=10
GET /api/books?genre=fantasy&sort=title
```
## ✅ Correct
- `GET /api/books` → `200 OK` with JSON array
- `POST /api/books` → `201 Created` with created object in body
- `DELETE /api/books/5` → `204 No Content` without body
- `GET /api/books?page=3` → pagination via query params
## ❌ Incorrect
- `GET /api/getBooks` — verb in the URL
- `POST /api/book` — singular resource
- `POST /api/books` returning `200` — must be `201`
- `DELETE /api/books/5` returning `200` — must be `204`
- `/books` without the `/api` prefix
- Response in XML when the API is JSON
- `/api/books/page/3` — filter goes in query param, not in the path
