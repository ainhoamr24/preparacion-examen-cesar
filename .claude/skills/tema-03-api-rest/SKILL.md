<!-- Main skill for Topic 03. Defines knowledge about REST APIs: HTTP verbs, status codes, endpoint design and anti-patterns. Entry point for loading this topic. -->
---
name: tema-03-api-rest
description: Knowledge for Topic 03 on REST APIs. Covers the HTTP protocol, REST services (GET/POST/PUT/DELETE/PATCH verbs and their semantics), JSON format, HTTP status code families (1xx-5xx) and endpoint design best practices. Load it for any question or implementation related to REST, HTTP or API design.
---
# Topic 03 — REST API
## Files in this skill
| File | Content |
|------|---------|
| `references/http-verbos-codigos.md` | Complete reference for HTTP verbs, status codes and JSON format |
| `examples/endpoints.md` | Canonical examples of correct and incorrect REST endpoints |
---
## HTTP and client-server architecture
Every web application is based on a **client-server** architecture:
- The **client** sends an HTTP request with: resource URL, headers and parameters.
- The **server** responds with: status code, response headers and the resource.
A **URL** has three parts:
1. Protocol (`http` or `https`)
2. Domain name (identifies the server)
3. Path/URI (identifies the resource within the server)
Example: `http://myserver.com/books?id=123`
- `http` → protocol
- `myserver.com` → domain
- `books?id=123` → URI; text after `?` are additional parameters
---
## REST services
**REST** (REpresentational State Transfer) — architectural style for distributed systems.
- Each resource is identified by a **URI**.
- Operations are defined by the **HTTP verb**, not the URL.
- Responses are typically **JSON**.
### HTTP verbs and semantics
| Verb | Use | Success code |
|------|-----|-------------|
| `GET` | Retrieve one or more resources | `200 OK` |
| `POST` | Create a new resource | `201 Created` |
| `PUT` | Replace a complete resource | `200 OK` |
| `PATCH` | Partially modify a resource | `200 OK` |
| `DELETE` | Delete a resource | `204 No Content` |
---
## Endpoints — best practices
- Use **nouns**, never verbs (`/books`, not `/getBooks`).
- Resources in **plural** even when returning a single one.
- To relate resources, **nest** in the URL.
- All endpoints start with `/api`.
- Optional parameters as query params, not in the path.
---
## Anti-patterns — MUST NOT
- ❌ Verbs in the URL: `/getBooks`, `/createUser`
- ❌ Singular resources: `/book`
- ❌ `POST` returning `200` instead of `201`
- ❌ `DELETE` returning `200` instead of `204`
- ❌ Endpoints without the `/api` prefix
- ❌ Responses in a format other than JSON
