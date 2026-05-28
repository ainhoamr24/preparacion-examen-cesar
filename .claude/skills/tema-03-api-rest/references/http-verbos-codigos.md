<!-- Reference for Topic 03. Complete table of HTTP verbs with their success codes, status code families (1xx-5xx) with the most important ones, and JSON format examples for objects, arrays and error responses. -->
# Reference: HTTP — Verbs, Status Codes and JSON
## HTTP verbs
| Verb | Semantic use | Success code | Body in request? | Body in response? |
|------|-------------|-------------|-----------------|------------------|
| `GET` | Read resource(s) | `200` | No | Yes (the resource) |
| `POST` | Create resource | `201` | Yes | Yes (created resource) |
| `PUT` | Replace full resource | `200` | Yes | Yes (updated resource) |
| `PATCH` | Partially modify | `200` | Yes | Yes (updated resource) |
| `DELETE` | Delete resource | `204` | No | No |
| `HEAD` | Response headers only | `200` | No | No |
## HTTP status code families
| Family | Meaning | Key examples |
|--------|---------|-------------|
| `1xx` | Information — incomplete request | Rarely used |
| `2xx` | Success | `200 OK`, `201 Created`, `204 No Content` |
| `3xx` | Redirection | `301 Moved Permanently`, `304 Not Modified` |
| `4xx` | **Client** error | `400 Bad Request`, `401 Unauthorized`, `404 Not Found` |
| `5xx` | **Server** error | `500 Internal Server Error`, `504 Gateway Timeout` |
## Most important codes
| Code | Name | When to use |
|------|------|-------------|
| `200` | OK | GET, PUT, PATCH with success |
| `201` | Created | POST with success — resource created |
| `204` | No Content | DELETE with success — no body |
| `400` | Bad Request | Incorrect input data / invalid form |
| `401` | Unauthorized | Client not authenticated |
| `403` | Forbidden | Authenticated client without permission |
| `404` | Not Found | Resource not found |
| `409` | Conflict | Conflict (duplicate email, ISBN already exists…) |
| `500` | Internal Server Error | Unexpected server error |
| `504` | Gateway Timeout | Excessive response time |
## JSON format
JSON object:
```json
{
  "name": "Nacho",
  "age": 39
}
```
Array of JSON objects:
```json
[
  { "name": "Nacho", "age": 39 },
  { "name": "Mario", "age": 4 }
]
```
Standard API error format:
```json
{
  "error": "ResourceNotFoundException",
  "message": "Book with isbn 123 not found"
}
```
