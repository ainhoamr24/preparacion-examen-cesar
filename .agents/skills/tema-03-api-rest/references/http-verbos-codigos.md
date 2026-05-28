<!-- Referencia del Tema 03. Tabla completa de verbos HTTP con sus códigos de éxito, familias de códigos de estado (1xx-5xx) con los más importantes, y ejemplos del formato JSON para objetos, arrays y respuestas de error. -->
# Referencia: HTTP — Verbos, Códigos de Estado y JSON
## Verbos HTTP
| Verbo | Uso semántico | Código éxito | ¿Cuerpo en request? | ¿Cuerpo en response? |
|-------|--------------|--------------|---------------------|----------------------|
| `GET` | Leer recurso/s | `200` | No | Sí (el recurso) |
| `POST` | Crear recurso | `201` | Sí | Sí (recurso creado) |
| `PUT` | Reemplazar recurso completo | `200` | Sí | Sí (recurso actualizado) |
| `PATCH` | Modificar parcialmente | `200` | Sí | Sí (recurso actualizado) |
| `DELETE` | Eliminar recurso | `204` | No | No |
| `HEAD` | Solo cabeceras de la respuesta | `200` | No | No |
## Códigos de estado HTTP — familias
| Familia | Significado | Ejemplos clave |
|---------|-------------|---------------|
| `1xx` | Información — petición incompleta | Poco habituales |
| `2xx` | Éxito | `200 OK`, `201 Created`, `204 No Content` |
| `3xx` | Redirección | `301 Moved Permanently`, `304 Not Modified` |
| `4xx` | Error del **cliente** | `400 Bad Request`, `401 Unauthorized`, `404 Not Found` |
| `5xx` | Error del **servidor** | `500 Internal Server Error`, `504 Gateway Timeout` |
## Códigos más importantes
| Código | Nombre | Cuándo usarlo |
|--------|--------|---------------|
| `200` | OK | GET, PUT, PATCH con éxito |
| `201` | Created | POST con éxito — recurso creado |
| `204` | No Content | DELETE con éxito — sin cuerpo |
| `400` | Bad Request | Datos de entrada incorrectos / formulario inválido |
| `401` | Unauthorized | Cliente no autenticado |
| `403` | Forbidden | Cliente autenticado pero sin permiso |
| `404` | Not Found | Recurso no encontrado |
| `409` | Conflict | Conflicto (email duplicado, ISBN ya existe…) |
| `500` | Internal Server Error | Error inesperado del servidor |
| `504` | Gateway Timeout | Tiempo de respuesta excesivo |
## Formato JSON
Objeto JSON:
```json
{
  "nombre": "Nacho",
  "edad": 39
}
```
Array de objetos JSON:
```json
[
  { "nombre": "Nacho", "edad": 39 },
  { "nombre": "Mario", "edad": 4 }
]
```
Formato de error estándar de la API:
```json
{
  "error": "ResourceNotFoundException",
  "message": "Book with isbn 123 not found"
}
```
