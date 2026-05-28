<!-- Skill principal del Tema 08. Define el conocimiento sobre la capa de presentación: modelos Summary y Detail con record Java, ResponseEntity para controlar el código HTTP, @RestControllerAdvice para centralizar excepciones, paginación con PaginatedResponse, gestión de idioma con Accept-Language y separación por roles (user/admin). -->
---
name: tema-08-capa-presentacion
description: Conocimiento del Tema 08 sobre la Capa de Presentación. Cubre los modelos de presentación (Summary y Detail como records Java), ResponseEntity para control del código HTTP, el tratamiento centralizado de excepciones con @RestControllerAdvice y @ExceptionHandler, los modelos de entrada (request records con @JsonFormat), la paginación con PaginatedResponse y @RequestParam, la gestión de idioma con Accept-Language e interceptores Spring, y la separación por roles (user/admin con packages específicos). Cárgalo para cualquier pregunta o implementación sobre controladores REST, manejo de excepciones o respuestas HTTP.
---
# Tema 08 — Capa de Presentación
## Ficheros de este skill
| Fichero | Contenido |
|---------|-----------|
| `references/response-entity-advice.md` | Referencia de ResponseEntity, @RestControllerAdvice, modelos request/response, paginación e idioma |
| `examples/controller-completo.md` | Ejemplo completo de controlador con ResponseEntity, handler de excepciones y paginación |
---
## Responsabilidad
La capa de presentación gestiona la interacción con el cliente:
- Recibe solicitudes HTTP.
- Valida datos de entrada básicos.
- Delega en la capa de dominio.
- Devuelve respuestas JSON con el código de estado correcto.
**MUST** ser independiente de lógica de negocio y persistencia.
---
## Modelos de presentación — Summary y Detail
Se crean modelos específicos para la capa de presentación usando `record` de Java. Dos tipos:
- **Summary** — para listados (solo campos básicos).
- **Detail** — para el detalle de un recurso (todos los campos).
Packages:
- `controller/webModel/response/` — modelos de respuesta.
- `controller/webModel/request/` — modelos de entrada.
---
## Excepciones — @RestControllerAdvice
**MUST NOT** usar `try/catch` en controladores para excepciones de negocio.
**MUST** usar `@RestControllerAdvice` para centralizar el manejo de errores.
`@RestControllerAdvice` = `@ControllerAdvice` + `@ResponseBody` automático → para APIs REST.
---
## Idioma — Accept-Language
Gestión dinámica del idioma mediante la cabecera HTTP `Accept-Language`:
1. `ThreadLocal<Locale>` en `LanguageUtils` — almacena el idioma por hilo.
2. Interceptor `CustomLocaleChangeInterceptor` — lee la cabecera y llama a `LanguageUtils`.
3. `LocaleConfig` — registra el interceptor en Spring.
---
## Roles — user/admin
Para distintos tipos de usuario se crean packages separados:
- `user/` — modelos simplificados, vista de lectura.
- `admin/` — modelos completos con todos los idiomas, vista de gestión.
- `common/` — componentes compartidos (PaginatedResponse…).
