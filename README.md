# EXAMEN-CESAR

Base de proyecto Spring Boot para preparar el examen de arquitectura backend, REST y persistencia.

## Referencia usada

La documentación de este repositorio toma como ejemplo estructural:

- [isma-MR/RepasoExamenCesar](https://github.com/isma-MR/RepasoExamenCesar)

No se copia ese repositorio literalmente; se usa para evitar describir una estructura inventada.

## Estructura de referencia

La referencia organiza el código principal alrededor de estos paquetes:

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

## Idea arquitectónica

- `controller`: expone endpoints REST y delega.
- `domain/model`: representa el dominio.
- `domain/repository`: define contratos del dominio.
- `domain/service`: declara casos de uso.
- `domain/service/impl`: implementa la lógica de negocio.
- `domain/service/dto`: mueve datos entre capas.
- `mapper`: transforma entre entidades de persistencia y DTOs/modelos.
- `persistence/dao`: acceso JPA de bajo nivel.
- `persistence/repository`: adapta persistencia a los contratos de `domain/repository`.
- `config` y `exception`: configuración y excepciones compartidas.

## Estado actual

Este repositorio todavía está en fase base. La documentación se ha corregido para que futuras implementaciones sigan una estructura coherente con la referencia.

## Comandos

```bash
./mvnw test
./mvnw spring-boot:run
```
