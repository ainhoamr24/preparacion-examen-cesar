# AGENTS.md — EXAMEN-CESAR

## Principio de trabajo

Antes de generar código o documentación:

- revisar la estructura real del repositorio local
- contrastarla con la referencia
- no inventar paquetes, capas ni flujos

## Arquitectura de referencia

La referencia se organiza así:

```text
src/main/java/com/.../
├── config/
├── controller/
├── domain/
│   ├── model/
│   ├── repository/
│   ├── service/
│   │   ├── dto/
│   │   └── impl/
│   └── validation/
├── exception/
├── mapper/
└── persistence/
    ├── dao/
    │   └── jpa/
    │       ├── entity/
    │       └── impl/
    └── repository/
```

## Lectura correcta de esa estructura

- `controller` expone endpoints.
- `domain` concentra modelo, contratos y servicios.
- `persistence` implementa acceso a datos y adapta JPA.
- `mapper` centraliza conversiones.
- `exception` reúne excepciones reutilizables.
- `config` contiene configuración de Spring.

## Instrucciones para agentes

- No describir la arquitectura como `presentation/application/infrastructure` si el proyecto no la usa.
- Si se implementa una funcionalidad nueva, respetar el recorrido `controller -> domain/service -> domain/repository -> persistence`.
- Si hace falta DTO, ubicarlo en `domain/service/dto` salvo que el repositorio local adopte otra convención explícita.
- Si hace falta implementación de servicio, ubicarla en `domain/service/impl`.
- Si hace falta adaptación JPA, separar `dao/jpa/entity`, `dao/jpa/impl` y `persistence/repository`.
- Si una clase de la referencia está vacía o incompleta, usarla solo como pista de ubicación, no como modelo de calidad.

## Convenciones mínimas

- Inyección por constructor.
- Controladores sin lógica de negocio.
- Excepciones de negocio fuera del controlador.
- Mapeos fuera del controlador.
- Código compilable siempre.

## Testing

Seguir el ejemplo de carpetas del repositorio de referencia:

- `src/test/java/.../domain/service/impl/` para unitarios de servicio
- `src/test/java/.../persistence/repository/` para persistencia
- `src/test/resources/` para configuración y datos de prueba

## Restricción

Si la documentación local contradice la estructura observada en el repositorio de referencia, debe corregirse antes de seguir generando más código.
