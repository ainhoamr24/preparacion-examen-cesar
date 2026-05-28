# CLAUDE.md — EXAMEN-CESAR

Estas instrucciones describen cómo trabajar en este repositorio tomando como referencia estructural.

## Objetivo

Construir un backend Spring Boot didáctico y mantenible para practicar:

- APIs REST
- separación de responsabilidades
- persistencia con JPA
- testing de servicios y persistencia

## Stack esperado

- Java 17
- Spring Boot 3.x
- Maven
- Spring Web
- Spring Data JPA
- Jakarta Validation
- JUnit 5 + Mockito + AssertJ

## Regla principal

No describir ni implementar una arquitectura distinta a la que se esté usando realmente en el proyecto. Si falta contexto funcional, parar y pedirlo.

## Estructura a seguir

La referencia no usa la estructura `presentation/application/infrastructure` que se había documentado antes. La estructura que debe tomarse como base es esta:

```text
com.examencesar/
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

## Responsabilidades

- `controller`: recibe HTTP, valida entrada y delega al servicio.
- `domain/model`: representa entidades y objetos del dominio.
- `domain/repository`: contratos que necesita el dominio.
- `domain/service`: interfaz de casos de uso.
- `domain/service/impl`: implementación de lógica de negocio.
- `domain/service/dto`: DTOs de entrada y salida.
- `mapper`: conversiones entre JPA, DTOs y modelos.
- `persistence/dao`: operaciones JPA y acceso de bajo nivel.
- `persistence/repository`: implementación de contratos de `domain/repository`.
- `config`: configuración de Spring.
- `exception`: excepciones de negocio y de recurso no encontrado.

## Reglas de implementación

- Usar inyección por constructor.
- Evitar `@Autowired` en campo.
- No meter lógica de negocio en el controlador.
- No acceder desde controlador directamente a persistencia.
- No devolver entidades de persistencia sin mapear si el caso pide DTO.
- Mantener los mapeos fuera del controlador.
- Si se añade validación, ubicarla de forma coherente con `domain/validation`.
- Si se crean adaptadores JPA, separarlos de los contratos del dominio.

## REST

- Endpoints bajo `/api` cuando se exponga API pública.
- Recursos con sustantivos.
- `GET` devuelve `200`.
- `POST` devuelve `201` cuando crea recurso.
- `DELETE` devuelve `204` cuando elimina sin cuerpo.

## Testing

Tomar como referencia la distribución de tests del repositorio ejemplo:

- tests unitarios de servicios en `src/test/java/.../domain/service/impl/`
- tests de persistencia en `src/test/java/.../persistence/repository/`
- configuración de soporte de tests en `src/test/java/.../persistence/`

## Qué evitar

- Inventar paquetes que no existen en la línea arquitectónica elegida.
- Mezclar contrato de dominio con detalle JPA en la misma clase.
- Documentar como obligatoria una capa que el proyecto no usa.
- Reescribir la arquitectura sin autorización explícita.
