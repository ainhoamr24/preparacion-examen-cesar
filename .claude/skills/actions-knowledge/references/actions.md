# Referencia de acciones REST — EXAMEN-CESAR

## Verbos HTTP y semántica

| Verbo | Uso | Código de éxito |
|-------|-----|-----------------|
| `GET` | Obtener uno o varios recursos | `200 OK` |
| `POST` | Crear un nuevo recurso | `201 Created` |
| `PUT` | Reemplazar un recurso completo | `200 OK` |
| `PATCH` | Modificar parcialmente un recurso | `200 OK` |
| `DELETE` | Eliminar un recurso | `204 No Content` |

## Reglas de diseño REST

- Los endpoints se nombran con **sustantivos**, nunca verbos.
- Los recursos se expresan en **plural**.
- La lógica de la operación la define el **verbo HTTP**, no la URL.
- Todas las respuestas son **JSON**.
- Los códigos de estado HTTP **MUST** ser semánticamente correctos.
- **MUST** todos los endpoints comenzar por `/api`.

## Endpoints canónicos

```
GET     /api/books            → Lista todos los libros          → 200 OK
GET     /api/books/12         → Obtiene el libro con id 12      → 200 OK
POST    /api/books            → Crea un nuevo libro             → 201 Created
PUT     /api/books/12         → Reemplaza el libro con id 12    → 200 OK
PATCH   /api/books/12         → Modifica parcialmente           → 200 OK
DELETE  /api/books/12         → Elimina el libro con id 12      → 204 No Content
GET     /api/books/12/authors → Lista los autores del libro 12  → 200 OK
```

## Recursos anidados

Cuando un recurso pertenece a otro, se anida en la URL. Máximo 2 niveles de anidamiento.

## Paginación y filtros

Usar query params, nunca en el path:

```
GET /api/books?page=3&size=10
GET /api/books?genre=fantasy&sort=title
```

## Formato de respuesta de error

```json
{
  "message": "El recurso con id 42 no existe",
  "status": 404,
  "timestamp": "2025-11-25T20:43:00Z"
}
```

## Códigos de estado comunes

| Código | Cuándo usarlo |
|--------|---------------|
| `200 OK` | GET, PUT, PATCH con éxito |
| `201 Created` | POST con éxito (recurso creado) |
| `204 No Content` | DELETE con éxito |
| `400 Bad Request` | Datos de entrada inválidos |
| `404 Not Found` | Recurso no encontrado |
| `409 Conflict` | Conflicto (email duplicado, etc.) |
| `500 Internal Server Error` | Error inesperado del servidor |

## Ejemplos ✅/❌

- ✅ `GET /api/books` → `200 OK` con array JSON
- ✅ `POST /api/books` → `201 Created` con el objeto creado
- ✅ `DELETE /api/books/5` → `204 No Content` sin cuerpo
- ❌ `GET /api/getBooks` — verbo en la URL
- ❌ `POST /api/book` — recurso en singular
- ❌ `POST /api/books` devolviendo `200` — debería ser `201`
- ❌ `DELETE /api/books/5` devolviendo `200` — debería ser `204`
- ❌ `/books` sin el prefijo `/api`
- ❌ Respuesta en formato distinto a JSON
