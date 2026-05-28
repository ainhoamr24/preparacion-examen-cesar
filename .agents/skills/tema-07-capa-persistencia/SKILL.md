<!-- Skill principal del Tema 07. Define el conocimiento sobre la capa de persistencia: patrón DAO con interfaz genérica y DAOs específicos, anotaciones JPA, EntityManager, los tres tipos de acceso a datos (JPQL, Native SQL, Criteria API), mapeadores con MapStruct y testing con H2 y Flyway. -->
---
name: tema-07-capa-persistencia
description: Conocimiento del Tema 07 sobre la Capa de Persistencia. Cubre el patrón DAO con interfaces genéricas, Jakarta Persistence (JPA) con sus anotaciones (@Entity, @Table, @Id, @GeneratedValue, @Column, @ManyToOne, @OneToMany, @ManyToMany, @JoinTable), el EntityManager y sus operaciones CRUD, los tres métodos de acceso a datos (JPQL, Native SQL, Criteria API), los mapeadores con MapStruct, y el testing de persistencia con H2 y Flyway usando @DataJpaTest. Cárgalo para cualquier pregunta o implementación sobre JPA, repositorios o la capa de persistencia.
---
# Tema 07 — Capa de Persistencia
## Ficheros de este skill
| Fichero | Contenido |
|---------|-----------|
| `references/jpa-anotaciones.md` | Referencia completa de anotaciones JPA, EntityManager y tipos de acceso a datos |
| `examples/dao-repositorio.md` | Ejemplo completo de DAO genérico, DAO específico, repositorio y test con H2 |
---
## Responsabilidad
La capa de persistencia gestiona el acceso a datos:
- Trabaja con modelos de dominio, abstrayendo los detalles de almacenamiento.
- Implementa las interfaces de repositorio definidas en dominio.
- Usa DAOs para encapsular los detalles de JPA.
---
## Patrón DAO
El patrón DAO (Data Access Object) separa y encapsula el acceso a datos.
### Interfaz genérica
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
### DAOs específicos (extienden GenericDao)
```java
public interface BookDao extends GenericDao<BookEntity> {
    Optional<BookEntity> findByIsbn(String isbn);
    void deleteByIsbn(String isbn);
}
public interface PublisherDao extends GenericDao<PublisherEntity> {
    Optional<PublisherEntity> findBySlug(String slug);
}
```
---
## Testing de persistencia
```java
@DataJpaTest
@ContextConfiguration(classes = TestConfig.class)
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
class BookJpaDaoImplTest {
    @PersistenceContext private EntityManager entityManager;
    @Autowired private BookJpaDao bookJpaDao;
}
```
Configuración BD en memoria (`application-test.properties`):
```properties
spring.datasource.url=jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.H2Dialect
spring.jpa.hibernate.ddl-auto=none
spring.jpa.show-sql=true
```
Flyway ejecuta scripts de `src/main/resources/db/migration/` con formato `V1__init.sql`, `V2__datos.sql`.
