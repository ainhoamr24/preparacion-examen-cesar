<!-- Main skill for Topic 07. Defines knowledge about the persistence layer: DAO pattern with generic interface and specific DAOs, JPA annotations, EntityManager, the three data access types (JPQL, Native SQL, Criteria API), MapStruct mappers and testing with H2 and Flyway. -->
---
name: tema-07-capa-persistencia
description: Knowledge for Topic 07 on the persistence layer. Covers DAOs, JPA entities, `EntityManager`, adapter repositories and persistence testing with H2 and Flyway. Load it for any question or implementation about JPA, repositories or the persistence layer.
---
# Topic 07 — Persistence Layer
## Files in this skill
| File | Content |
|------|---------|
| `references/jpa-anotaciones.md` | Complete reference for JPA annotations, EntityManager and data access types |
| `examples/dao-repositorio.md` | Complete example of generic DAO, specific DAO, repository and H2 test |
---
## Responsibility
The persistence layer manages data access:
- Works with domain models, abstracting storage details.
- Implements the repository interfaces defined in the domain.
- Uses DAOs to encapsulate JPA details.
---
## DAO pattern
The DAO (Data Access Object) pattern separates and encapsulates data access.
### Generic interface
```java
public interface GenericDao<T> {
    List<T> findAll(int page, int size);
    Optional<T> findById(Long id);
    T insert(T entity);
    T update(T entity);
    void deleteById(Long id);
    long count();
}
```
### Specific DAOs
```java
public interface BookJpaDao extends GenericDao<BookJpaEntity> {
    Optional<BookJpaEntity> findByIsbn(String isbn);
    void deleteByIsbn(String isbn);
}
```
---
## Persistence testing
```java
@DataJpaTest
@ContextConfiguration(classes = TestConfig.class)
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
class BookJpaDaoImplTest {
    @PersistenceContext private EntityManager entityManager;
    @Autowired private BookJpaDao bookJpaDao;
}
```
Test in-memory DB configuration:
```properties
spring.datasource.url=jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.H2Dialect
spring.jpa.hibernate.ddl-auto=none
spring.jpa.show-sql=true
```
Flyway can run scripts from `src/test/resources/db/migration/` when the project is configured to do so.
