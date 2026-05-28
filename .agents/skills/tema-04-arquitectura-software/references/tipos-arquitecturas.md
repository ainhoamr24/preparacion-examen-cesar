<!-- Referencia del Tema 04. Tabla comparativa de los 7 tipos de arquitecturas (monolítica, capas, microservicios, hexagonal, cebolla, eventos, serverless) con sus ventajas e inconvenientes, y relación con la arquitectura limpia de Uncle Bob. -->
# Referencia: Tipos de Arquitecturas de Software
| Tipo | Descripción | Ventajas | Desventajas |
|------|-------------|----------|-------------|
| **Monolítica** | Todo el sistema como un único bloque | Simple al inicio | Difícil de mantener y escalar al crecer |
| **Por capas** | Capas con responsabilidades específicas (presentación, negocio, datos) | Muy popular, organizada, clara | Dependencias rígidas si no se gestiona bien |
| **Microservicios** | Servicios independientes que se comunican entre sí | Escalabilidad independiente, ciclos de vida separados | Complejidad de distribución y comunicación |
| **Hexagonal (Ports & Adapters)** | Lógica de negocio separada del exterior mediante puertos y adaptadores | Alta testabilidad, independencia del exterior | Mayor complejidad inicial |
| **Cebolla** | Capas concéntricas; el núcleo es la lógica de negocio | Desacoplamiento, similar a limpia | Mayor curva de aprendizaje |
| **Orientada a eventos** | Producción, detección, consumo y reacción a eventos | Útil para sistemas distribuidos y escalables | Difícil de depurar y razonar |
| **Serverless** | El proveedor de nube gestiona el escalado; el dev solo escribe código | Alta disponibilidad, bajo coste operativo | Dependencia del proveedor |
## Arquitecturas derivadas de la arquitectura limpia
La arquitectura limpia (Uncle Bob) dio lugar a varias arquitecturas que comparten sus principios:
- **Arquitectura por capas** — la más usada en este curso.
- **Arquitectura hexagonal** — puertos y adaptadores.
- **Arquitectura de cebolla** — capas concéntricas.
