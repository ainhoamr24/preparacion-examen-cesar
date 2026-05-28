---
name: actions-steps
description: Step-by-step implementation guide for the EXAMEN-CESAR project. Defines the correct implementation order following the `controller`, `domain`, `mapper` and `persistence` structure, how to verify each step and what to check before marking it as complete. Load it whenever you implement a new feature.
---
# actions-steps
Implementation guide for EXAMEN-CESAR. Defines the order and concrete steps to implement any new feature.
---
## Mandatory implementation order
Always implement in this order:
```text
1. Domain model (`domain/model/`)
2. DTOs and contracts (`domain/service/dto/`, `domain/repository/`)
3. Service: interface + implementation (`domain/service/`, `domain/service/impl/`)
4. Mapper (`mapper/`) if conversion is needed
5. Persistence (`persistence/dao/...`, `persistence/repository/`)
6. REST controller (`controller/`) if the feature exposes HTTP
7. Shared exceptions or validations (`exception/`, `domain/validation/`) if needed
8. Unit and persistence tests
```
---
## Step 1 — Domain model
Create the model in `src/main/java/com/examencesar/domain/model/`:
```java
public class <Entity> {
    private Long id;
    private String <field>;
}
```
**Verify:** the class represents the domain and does not depend on HTTP.
---
## Step 2 — Contracts and DTOs
Create the repository contract in `src/main/java/com/examencesar/domain/repository/`:
```java
public interface <Resource>Repository {
    Optional<<Resource>Dto> findById(Long id);
    <Resource>Dto save(<Resource>Dto dto);
}
```
Create DTOs in `src/main/java/com/examencesar/domain/service/dto/`:
```java
public record <Resource>Dto(Long id, String <field>) {}
```
**Verify:** contracts and DTOs are in the domain, not in invented packages.
---
## Step 3 — Service
### Interface in `src/main/java/com/examencesar/domain/service/`:
```java
public interface <Resource>Service {
    List<<Resource>Dto> findAll();
    Optional<<Resource>Dto> findById(Long id);
    <Resource>Dto create(<Resource>Dto dto);
    <Resource>Dto update(<Resource>Dto dto);
    void deleteById(Long id);
}
```
### Implementation in `src/main/java/com/examencesar/domain/service/impl/`:
```java
public class <Resource>ServiceImpl implements <Resource>Service {
    private final <Resource>Repository repository;

    public <Resource>ServiceImpl(<Resource>Repository repository) {
        this.repository = repository;
    }
}
```
**Verify:** the service uses `domain/repository` contracts and does not depend on the controller.
---
## Step 4 — Mapper
If conversion is needed, create a mapper in `src/main/java/com/examencesar/mapper/`:
```java
public class <Resource>Mapper {
    private static <Resource>Mapper INSTANCE;

    public static <Resource>Mapper getInstance() {
        if (INSTANCE == null) {
            INSTANCE = new <Resource>Mapper();
        }
        return INSTANCE;
    }
}
```
**Verify:** the mapper centralises conversions and contains no business logic.
---
## Step 5 — Persistence
Persistence is split across:

- `src/main/java/com/examencesar/persistence/dao/jpa/entity/`
- `src/main/java/com/examencesar/persistence/dao/jpa/impl/`
- `src/main/java/com/examencesar/persistence/repository/`

Example:
```java
public class <Resource>RepositoryImpl implements <Resource>Repository {
    private final <Resource>JpaDao <resource>JpaDao;

    public <Resource>RepositoryImpl(<Resource>JpaDao <resource>JpaDao) {
        this.<resource>JpaDao = <resource>JpaDao;
    }
}
```
**Verify:** the persistence implementation stays outside the domain.
---
## Step 6 — REST controller
If the feature exposes HTTP, create the controller in `src/main/java/com/examencesar/controller/`:
```java
@RestController
@RequestMapping("/api/<resources>")
public class <Resource>Controller {
    private final <Resource>Service <resource>Service;
}
```
**Verify:** the controller delegates and does not access `persistence`.
---
## Step 7 — Tests
Service unit tests in `src/test/java/com/examencesar/domain/service/impl/`:
```java
@ExtendWith(MockitoExtension.class)
class <Resource>ServiceTest {
    @Mock
    private <Resource>Repository repository;

    @InjectMocks
    private <Resource>ServiceImpl service;
}
```
Persistence tests in `src/test/java/com/examencesar/persistence/repository/`.
---
## Final verification for each step
Before marking a step as complete, check:
- [ ] Does the code compile? (`./mvnw compile`)
- [ ] No unused imports?
- [ ] Is the class in the correct package according to the project structure?
- [ ] Do the step's tests pass?
