---
name: actions-steps
description: Guía de implementación paso a paso para el proyecto EXAMEN-CESAR. Define el orden correcto de implementación siguiendo la estructura de `controller`, `domain`, `mapper` y `persistence`, cómo verificar cada paso y qué comprobar antes de darlo por completado. Cárgalo siempre que implementes una nueva funcionalidad.
---
# actions-steps
Guía de implementación de EXAMEN-CESAR. Define el orden y los pasos concretos para implementar cualquier funcionalidad nueva.
---
## Orden obligatorio de implementación
Implementa siempre en este orden:
```text
1. Modelo de dominio (`domain/model/`)
2. DTOs y contratos (`domain/service/dto/`, `domain/repository/`)
3. Servicio: interfaz + implementación (`domain/service/`, `domain/service/impl/`)
4. Mapper (`mapper/`) si hace falta conversión
5. Persistencia (`persistence/dao/...`, `persistence/repository/`)
6. Controlador REST (`controller/`) si la funcionalidad expone HTTP
7. Excepciones o validaciones compartidas (`exception/`, `domain/validation/`) si hacen falta
8. Tests unitarios y de persistencia
```
---
## Paso 1 — Modelo de dominio
Crea el modelo en `src/main/java/com/examencesar/domain/model/`:
```java
public class <Entidad> {
    private Long id;
    private String <campo>;
}
```
**Verifica:** la clase representa el dominio y no depende de HTTP.
---
## Paso 2 — Contratos y DTOs
Crea el contrato del repositorio en `src/main/java/com/examencesar/domain/repository/`:
```java
public interface <Recurso>Repository {
    Optional<<Recurso>Dto> findById(Long id);
    <Recurso>Dto save(<Recurso>Dto dto);
}
```
Crea los DTOs en `src/main/java/com/examencesar/domain/service/dto/`:
```java
public record <Recurso>Dto(Long id, String <campo>) {}
```
**Verifica:** contratos y DTOs están en dominio, no en paquetes inventados.
---
## Paso 3 — Servicio
### Interfaz en `src/main/java/com/examencesar/domain/service/`:
```java
public interface <Recurso>Service {
    List<<Recurso>Dto> findAll();
    Optional<<Recurso>Dto> findById(Long id);
    <Recurso>Dto create(<Recurso>Dto dto);
    <Recurso>Dto update(<Recurso>Dto dto);
    void deleteById(Long id);
}
```
### Implementación en `src/main/java/com/examencesar/domain/service/impl/`:
```java
public class <Recurso>ServiceImpl implements <Recurso>Service {
    private final <Recurso>Repository repository;

    public <Recurso>ServiceImpl(<Recurso>Repository repository) {
        this.repository = repository;
    }
}
```
**Verifica:** el servicio usa contratos de `domain/repository` y no depende del controlador.
---
## Paso 4 — Mapper
Si hace falta conversión, crea un mapper en `src/main/java/com/examencesar/mapper/`:
```java
public class <Recurso>Mapper {
    private static <Recurso>Mapper INSTANCE;

    public static <Recurso>Mapper getInstance() {
        if (INSTANCE == null) {
            INSTANCE = new <Recurso>Mapper();
        }
        return INSTANCE;
    }
}
```
**Verifica:** el mapper centraliza conversiones y no contiene lógica de negocio.
---
## Paso 5 — Persistencia
La persistencia se reparte en:

- `src/main/java/com/examencesar/persistence/dao/jpa/entity/`
- `src/main/java/com/examencesar/persistence/dao/jpa/impl/`
- `src/main/java/com/examencesar/persistence/repository/`

Ejemplo:
```java
public class <Recurso>RepositoryImpl implements <Recurso>Repository {
    private final <Recurso>JpaDao <recurso>JpaDao;

    public <Recurso>RepositoryImpl(<Recurso>JpaDao <recurso>JpaDao) {
        this.<recurso>JpaDao = <recurso>JpaDao;
    }
}
```
**Verifica:** la implementación de persistencia queda fuera del dominio.
---
## Paso 6 — Controlador REST
Si la funcionalidad expone HTTP, crea el controlador en `src/main/java/com/examencesar/controller/`:
```java
@RestController
@RequestMapping("/api/<recursos>")
public class <Recurso>Controller {
    private final <Recurso>Service <recurso>Service;
}
```
**Verifica:** el controlador delega y no accede a `persistence`.
---
## Paso 7 — Tests
Tests unitarios del servicio en `src/test/java/com/examencesar/domain/service/impl/`:
```java
@ExtendWith(MockitoExtension.class)
class <Recurso>ServiceTest {
    @Mock
    private <Recurso>Repository repository;

    @InjectMocks
    private <Recurso>ServiceImpl service;
}
```
Tests de persistencia en `src/test/java/com/examencesar/persistence/repository/`.
---
## Verificación final de cada paso
Antes de marcar un paso como completado, comprueba:
- [ ] ¿El código compila? (`./mvnw compile`)
- [ ] ¿No hay imports sin usar?
- [ ] ¿La clase está en el paquete correcto según la estructura del proyecto?
- [ ] ¿Los tests del paso pasan?
