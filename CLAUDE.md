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

- Tests unitarios de modelo en `src/test/java/.../domain/`: sin mocks, `@ParameterizedTest` + `@CsvSource` para múltiples escenarios.
- Tests unitarios de mapeadores en `src/test/java/.../mapper/`: sin mocks, patrón Arrange/Act/Assert, `assertAll` para todos los campos; probar que mapear `null` lanza `BusinessException`.
- Tests unitarios de servicios en `src/test/java/.../domain/service/impl/`: `@ExtendWith(MockitoExtension.class)`, `@Mock` sobre el repositorio de dominio, `@InjectMocks` sobre el servicio, `Mockito.verify()` para confirmar interacciones.
- Tests de validación de DTOs en `src/test/java/.../domain/service/dto/`: `@ParameterizedTest` + `@CsvSource`, verificar que datos inválidos lanzan `ValidationException` vía `DtoValidator.validate(dto)`.
- Tests de persistencia (DAO JPA) en `src/test/java/.../persistence/dao/jpa/impl/`: `@DataJpaTest` + `@ContextConfiguration(TestConfig.class)` + `@AutoConfigureTestDatabase(replace = NONE)`, H2 en memoria, `@PersistenceContext EntityManager` para preparar datos, `assertAll` para verificar resultados.
- `TestConfig` en `src/test/java/.../persistence/`: clase `@Configuration` con `@EnableJpaRepositories`, `@EntityScan` y `@Bean` por cada DAO JPA.
- Migraciones Flyway en `src/main/resources/db/migration/` con formato `V1__init.sql`, `V2__insert_data.sql`, etc.; configuración H2 en `src/test/resources/application-test.properties`.


## Qué evitar

- Inventar paquetes que no existen en la línea arquitectónica elegida.
- Mezclar contrato de dominio con detalle JPA en la misma clase.
- Documentar como obligatoria una capa que el proyecto no usa.
- Reescribir la arquitectura sin autorización explícita.
