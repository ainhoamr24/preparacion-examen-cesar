<!-- Skill principal del Tema 03. Define el conocimiento sobre APIs REST: verbos HTTP, códigos de estado, diseño de endpoints y anti-patrones. Es el punto de entrada para cargar este tema. -->
---
name: tema-03-api-rest
description: Conocimiento del Tema 03 sobre APIs REST. Cubre el protocolo HTTP, los servicios REST (verbos GET/POST/PUT/DELETE/PATCH y su semántica), el formato JSON, los códigos de estado HTTP agrupados por familia (1xx-5xx) y las buenas prácticas de diseño de endpoints. Cárgalo para cualquier pregunta o implementación relacionada con REST, HTTP o diseño de APIs.
---
# Tema 03 — API REST
## Ficheros de este skill
| Fichero | Contenido |
|---------|-----------|
| `references/http-verbos-codigos.md` | Referencia completa de verbos HTTP, códigos de estado y formato JSON |
| `examples/endpoints.md` | Ejemplos canónicos de endpoints REST correctos e incorrectos |
---
## HTTP y arquitectura cliente-servidor
Toda aplicación web se basa en una arquitectura **cliente-servidor**:
- El **cliente** envía una petición HTTP con: URL del recurso, cabeceras y parámetros.
- El **servidor** responde con: código de estado, cabeceras de respuesta y el recurso.
Una **URL** tiene tres partes:
1. Protocolo (`http` o `https`)
2. Nombre de dominio (identifica al servidor)
3. Ruta/URI (identifica el recurso dentro del servidor)
Ejemplo: `http://miservidor.com/books?id=123`
- `http` → protocolo
- `miservidor.com` → dominio
- `books?id=123` → URI; el texto tras `?` son parámetros adicionales
---
## Servicios REST
**REST** (REpresentational State Transfer) — estilo de arquitectura para sistemas distribuidos.
- Cada recurso se identifica con una **URI**.
- Las operaciones las define el **verbo HTTP**, no la URL.
- Las respuestas son típicamente **JSON**.
### Verbos HTTP y semántica
| Verbo | Uso | Código de éxito |
|-------|-----|-----------------|
| `GET` | Obtener uno o varios recursos | `200 OK` |
| `POST` | Crear un nuevo recurso | `201 Created` |
| `PUT` | Reemplazar un recurso completo | `200 OK` |
| `PATCH` | Modificar parcialmente un recurso | `200 OK` |
| `DELETE` | Eliminar un recurso | `204 No Content` |
---
## Endpoints — buenas prácticas
- Usar **sustantivos**, nunca verbos (`/books`, no `/getBooks`).
- Los recursos en **plural** aunque se devuelva uno solo.
- Para relacionar recursos, **anidar** en la URL.
- Todos los endpoints empiezan por `/api`.
- Parámetros opcionales como query params, no en el path.
---
## Anti-patrones — MUST NOT
- ❌ Verbos en la URL: `/getBooks`, `/createUser`
- ❌ Recursos en singular: `/book`
- ❌ `POST` devolviendo `200` en vez de `201`
- ❌ `DELETE` devolviendo `200` en vez de `204`
- ❌ Endpoints sin el prefijo `/api`
- ❌ Respuestas en formato distinto a JSON
