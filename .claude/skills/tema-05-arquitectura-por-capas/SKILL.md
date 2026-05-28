<!-- Skill principal del Tema 05. Define el conocimiento sobre arquitectura por capas: las tres capas (presentación, dominio, persistencia), sus responsabilidades, la inversión de dependencias moviendo interfaces de repositorio al dominio, y las reglas absolutas de comunicación entre capas. -->
---
name: tema-05-arquitectura-por-capas
description: Conocimiento del Tema 05 sobre Arquitectura por Capas. Cubre el patrón de capas (presentación/controller, dominio, persistencia), las responsabilidades de cada capa, las anotaciones Spring Boot de presentación (@RestController, @RequestMapping, @GetMapping, etc.), la estructura de dominio sin dependencias externas, la inversión de dependencias (interfaces de repositorio en dominio), y las reglas de comunicación entre capas. Cárgalo para cualquier pregunta o implementación sobre la estructura de capas.
---
# Tema 05 — Arquitectura por Capas
## Ficheros de este skill
| Fichero | Contenido |
|---------|-----------|
| `references/capas-responsabilidades.md` | Tabla detallada de responsabilidades, lo que SÍ y NO va en cada capa |
| `examples/controller-service-repo.md` | Ejemplos de código de cada capa con sus anotaciones Spring |
---
## Las tres capas básicas
```
┌──────────────────────────┐
│   controller (presentation) │  Gestiona HTTP: recibe, delega, responde
├──────────────────────────┤
│         domain           │  Lógica de negocio, modelos, servicios
├──────────────────────────┤
│       persistence        │  Acceso a datos (CRUD)
└──────────────────────────┘
```
Las capas superiores usan las inferiores. Las inferiores **NO** dependen de las superiores.
---
## Inversión de dependencias
Sin inversión: `controller → domain → persistence` (todo depende de persistencia).
Con inversión: las **interfaces de repositorio se mueven a dominio**.
```
controller → domain ← persistence
```
Resultado: persistencia depende de dominio, no al revés. Se puede cambiar la BD sin tocar dominio.
---
## Reglas absolutas — MUST NOT
- **MUST NOT** poner lógica de negocio en la capa de presentación.
- **MUST NOT** que dominio importe clases de Spring, JPA u otras dependencias externas.
- **MUST NOT** que persistencia tenga lógica de negocio.
- **MUST NOT** saltar capas (controlador accediendo directamente a persistencia).
