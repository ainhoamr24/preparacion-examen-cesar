---
name: actions-knowledge
description: Conocimiento de dominio del proyecto EXAMEN-CESAR sobre Spring Boot, diseño REST y organización por paquetes inspirada en la referencia del proyecto. Cubre controladores, servicios de dominio, DTOs, mappers, persistencia y nomenclatura. Cárgalo siempre que implementes o revises cualquier clase Java del proyecto.
---
# actions-knowledge
Conocimiento de dominio de EXAMEN-CESAR. Toda implementación Java **MUST** seguir estas convenciones.
## Ficheros de este skill
| Fichero | Contenido |
|---------|-----------|
| `references/actions.md` | Referencia completa de controladores REST: verbos HTTP, códigos de estado, estructura de endpoints y ejemplos correctos e incorrectos |
---
## Estructura del proyecto
La organización válida para este proyecto es la documentada en el repositorio local:
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
### Responsabilidades
| Paquete | Responsabilidad | MUST NOT |
|---------|-----------------|----------|
| `controller` | Recibir HTTP, validar, delegar y responder | Lógica de negocio, acceso directo a persistencia |
| `domain/model` | Representar el dominio | Mezclar detalles HTTP |
| `domain/repository` | Definir contratos del dominio | Depender de controladores |
| `domain/service` | Declarar casos de uso | Mezclar detalles HTTP |
| `domain/service/impl` | Implementar lógica de negocio | Acceder al controlador |
| `domain/service/dto` | Transportar datos entre capas | Contener lógica de persistencia |
| `mapper` | Convertir entre DTOs, modelo y JPA | Hacer lógica de negocio |
| `persistence/dao` | Acceso JPA de bajo nivel | Exponer HTTP |
| `persistence/repository` | Adaptar persistencia a contratos del dominio | Meter reglas de negocio |
| `config` y `exception` | Configuración y excepciones compartidas | Lógica de negocio compleja |
---
## Controladores REST
```java
@RestController
@RequestMapping("/api/<recursos>")
public class <Recurso>Controller {
    private final <Recurso>Service <recurso>Service;

    public <Recurso>Controller(<Recurso>Service <recurso>Service) {
        this.<recurso>Service = <recurso>Service;
    }
}
```
**Reglas del controlador:**
- **MUST** inyección por constructor.
- **MUST** validar entradas cuando corresponda.
- **MUST NOT** implementar lógica de negocio.
- **MUST NOT** inyectar clases de `persistence` directamente.
- **MUST NOT** capturar excepciones de negocio en el controlador.
---
## Servicios de dominio
```java
public interface <Recurso>Service {
    List<<Recurso>Dto> findAll();
    Optional<<Recurso>Dto> findById(Long id);
    <Recurso>Dto create(<Recurso>Dto dto);
    <Recurso>Dto update(<Recurso>Dto dto);
    void deleteById(Long id);
}

public class <Recurso>ServiceImpl implements <Recurso>Service {
    private final <Recurso>Repository repository;

    public <Recurso>ServiceImpl(<Recurso>Repository repository) {
        this.repository = repository;
    }
}
```
**Reglas:**
- **MUST** usar contratos de `domain/repository`.
- **MUST NOT** recibir objetos HTTP.
- **MUST NOT** mezclar detalles JPA en la interfaz del servicio.
---
## Persistencia
```java
public interface <Recurso>Repository {
    Optional<<Recurso>Dto> findById(Long id);
    <Recurso>Dto save(<Recurso>Dto dto);
}

public class <Recurso>RepositoryImpl implements <Recurso>Repository {
    private final <Recurso>JpaDao <recurso>JpaDao;
}
```
**Reglas:**
- Los contratos van en `domain/repository`.
- Las implementaciones van en `persistence/repository`.
- Las entidades JPA van en `persistence/dao/jpa/entity`.
- Los DAOs JPA van en `persistence/dao/jpa/impl`.
---
## DTOs y mappers
```java
public record <Recurso>Dto(Long id, String nombre) {}
```
- Los DTOs van en `domain/service/dto`.
- Los mappers van en `mapper`.
- No inventar tipos intermedios que no existan en el proyecto.
---
## Excepciones
```java
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}
```
---
## Nomenclatura
| Elemento | Convención | Ejemplo |
|----------|------------|---------|
| Clases | `PascalCase` | `BookService`, `UserDto` |
| Variables y métodos | `camelCase` | `bookTitle`, `findById` |
| Constantes | `UPPER_SNAKE_CASE` | `MAX_RETRIES` |
| Paquetes | `lowercase` | `com.examencesar.domain.service` |
| Endpoints (URL) | `kebab-case` | `/api/book-authors` |
---
## Anti-patrones — MUST NOT
- ❌ `@Autowired` en campo.
- ❌ Lógica de negocio en el controlador.
- ❌ Controlador inyectando `persistence` directamente.
- ❌ Servicio recibiendo objetos HTTP.
- ❌ Mezclar contrato de dominio y detalle JPA en la misma clase.
- ❌ Tipos o paquetes no existentes en la estructura del proyecto.
