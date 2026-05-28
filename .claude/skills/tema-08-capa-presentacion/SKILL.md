<!-- Main skill for Topic 08. Defines knowledge about REST controllers and HTTP responses without introducing models not backed by the project. -->
---
name: tema-08-capa-presentacion
description: Knowledge for Topic 08 on the presentation layer. Covers REST controllers, `ResponseEntity`, input validation and basic error handling. Load it for any question or implementation about REST controllers, exception handling or HTTP responses.
---
# Topic 08 — Presentation Layer
## Files in this skill
| File | Content |
|------|---------|
| `references/response-entity-advice.md` | Reference for `ResponseEntity`, HTTP responses and simple exceptions |
| `examples/controller-completo.md` | Complete REST controller example aligned with the reference |
---
## Responsibility
The presentation layer:
- receives HTTP requests
- validates basic input data
- delegates to services
- returns JSON responses with the correct code

**MUST NOT** contain business logic or access persistence directly.
---
## Rules
- Endpoints under `/api`.
- Controllers in `controller/`.
- Responses with `ResponseEntity` when the HTTP code needs to be expressed explicitly.
- Avoid business `try/catch` inside the controller.
