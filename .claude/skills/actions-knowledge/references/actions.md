# REST Actions Reference — EXAMEN-CESAR

## HTTP verbs and semantics

| Verb | Use | Success code |
|------|-----|-------------|
| `GET` | Retrieve one or more resources | `200 OK` |
| `POST` | Create a new resource | `201 Created` |
| `PUT` | Replace a complete resource | `200 OK` |
| `PATCH` | Partially modify a resource | `200 OK` |
| `DELETE` | Delete a resource | `204 No Content` |

## REST design rules

- Endpoints are named with **nouns**, never verbs.
- Resources are expressed in **plural**.
- The logic of the operation is defined by the **HTTP verb**, not the URL.
- All responses are **JSON**.
- HTTP status codes **MUST** be semantically correct.
- **MUST** all endpoints start with `/api`.

## Canonical endpoints

```
GET     /api/books            → List all books              → 200 OK
GET     /api/books/12         → Get book with id 12         → 200 OK
POST    /api/books            → Create a new book           → 201 Created
PUT     /api/books/12         → Replace book with id 12     → 200 OK
PATCH   /api/books/12         → Partially modify            → 200 OK
DELETE  /api/books/12         → Delete book with id 12      → 204 No Content
GET     /api/books/12/authors → List authors of book 12     → 200 OK
```

## Nested resources

When a resource belongs to another, it is nested in the URL. Maximum 2 levels of nesting.

## Pagination and filters

Use query params, never in the path:

```
GET /api/books?page=3&size=10
GET /api/books?genre=fantasy&sort=title
```

## Error response format

```json
{
  "message": "Resource with id 42 does not exist",
  "status": 404,
  "timestamp": "2025-11-25T20:43:00Z"
}
```

## Common status codes

| Code | When to use |
|------|-------------|
| `200 OK` | GET, PUT, PATCH with success |
| `201 Created` | POST with success (resource created) |
| `204 No Content` | DELETE with success |
| `400 Bad Request` | Invalid input data |
| `404 Not Found` | Resource not found |
| `409 Conflict` | Conflict (duplicate email, etc.) |
| `500 Internal Server Error` | Unexpected server error |

## Examples ✅/❌

- ✅ `GET /api/books` → `200 OK` with JSON array
- ✅ `POST /api/books` → `201 Created` with the created object
- ✅ `DELETE /api/books/5` → `204 No Content` without body
- ❌ `GET /api/getBooks` — verb in the URL
- ❌ `POST /api/book` — singular resource
- ❌ `POST /api/books` returning `200` — should be `201`
- ❌ `DELETE /api/books/5` returning `200` — should be `204`
- ❌ `/books` without the `/api` prefix
- ❌ Response in a format other than JSON
