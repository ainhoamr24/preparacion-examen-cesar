<!-- Skill principal del Tema 04. Define el conocimiento sobre arquitectura de software: definición, componentes clave, objetivos, principios (separación de responsabilidades, bajo acoplamiento, alta cohesión) y tipos de arquitecturas. Es el punto de entrada para cargar este tema. -->
---
name: tema-04-arquitectura-software
description: Conocimiento del Tema 04 sobre Arquitectura de Software. Cubre la definición de arquitectura, sus componentes clave (componentes, conectores, relaciones), sus objetivos (complejidad, escalabilidad, mantenibilidad, flexibilidad), los principios clave (separación de responsabilidades, independencia tecnológica, modularidad, bajo acoplamiento y alta cohesión), la arquitectura limpia de Robert C. Martin y los tipos de arquitecturas (monolítica, capas, microservicios, hexagonal, cebolla, eventos, serverless). Cárgalo para cualquier pregunta teórica sobre arquitectura de software.
---
# Tema 04 — Arquitectura de Software
## Ficheros de este skill
| Fichero | Contenido |
|---------|-----------|
| `references/tipos-arquitecturas.md` | Tabla comparativa de todos los tipos de arquitecturas con ventajas e inconvenientes |
| `examples/arquitectura-limpia.md` | Descripción detallada de la arquitectura limpia de Uncle Bob y sus beneficios |
---
## Definición
La **arquitectura de software** es el conjunto de decisiones fundamentales sobre la estructura y el comportamiento de un sistema:
- Cómo se organizan los componentes.
- Cómo interactúan entre sí.
- Las restricciones y directrices que afectan esas interacciones.
### Componentes clave
| Elemento | Descripción |
|----------|-------------|
| **Componentes** | Piezas funcionales: módulos, servicios, bases de datos |
| **Conectores** | Cómo se comunican: HTTP, API, llamadas a procedimientos |
| **Relaciones** | Flujos de datos y dependencias entre componentes |
---
## Objetivos de una buena arquitectura
- **Gestión de la complejidad** — divide el sistema en partes más pequeñas.
- **Escalabilidad** — crece en tamaño sin perder eficiencia.
- **Mantenibilidad** — incorpora cambios sin romper lo existente.
- **Flexibilidad** — reemplaza componentes sin afectar al resto.
---
## Principios clave
| Principio | Descripción |
|-----------|-------------|
| **Separación de responsabilidades** | Cada componente tiene una única responsabilidad. Reduce acoplamiento. |
| **Independencia tecnológica** | El núcleo no depende de tecnologías concretas (BD, frameworks). |
| **Modularidad** | Módulos independientes: se desarrollan, prueban y despliegan por separado. |
| **Bajo acoplamiento** | Pocas dependencias entre componentes. |
| **Alta cohesión** | Componentes organizados en torno a una responsabilidad específica. |
| **Escalabilidad y rendimiento** | Diseño desde el inicio para manejar crecimiento. |
**En este curso se usa:** arquitectura limpia por capas.
