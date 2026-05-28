<!-- Skill principal del Tema 08. Define el conocimiento sobre controladores REST y respuestas HTTP sin introducir modelos no respaldados por el proyecto. -->
---
name: tema-08-capa-presentacion
description: Conocimiento del Tema 08 sobre la capa de presentación. Cubre controladores REST, `ResponseEntity`, validación de entrada y manejo básico de errores. Cárgalo para cualquier pregunta o implementación sobre controladores REST, manejo de excepciones o respuestas HTTP.
---
# Tema 08 — Capa de Presentación
## Ficheros de este skill
| Fichero | Contenido |
|---------|-----------|
| `references/response-entity-advice.md` | Referencia de `ResponseEntity`, respuestas HTTP y excepciones simples |
| `examples/controller-completo.md` | Ejemplo completo de controlador REST alineado con la referencia |
---
## Responsabilidad
La capa de presentación:
- recibe solicitudes HTTP
- valida datos de entrada básicos
- delega en servicios
- devuelve respuestas JSON con el código correcto

**MUST NOT** contener lógica de negocio ni acceder a persistencia directamente.
---
## Reglas
- Endpoints bajo `/api`.
- Controladores en `controller/`.
- Respuestas con `ResponseEntity` cuando el código HTTP necesite expresarse de forma explícita.
- Evitar `try/catch` de negocio dentro del controlador.
