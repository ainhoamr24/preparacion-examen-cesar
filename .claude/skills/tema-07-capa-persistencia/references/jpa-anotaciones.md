<!-- Reference for Topic 07. Tables of basic JPA annotations (@Entity, @Id, @Column…) and relationship annotations (@ManyToOne, @OneToMany, @ManyToMany with recommendation to break it), EntityManager operations (persist, merge, remove, flush), the three query methods with examples (JPQL, Native SQL, Criteria API) and MapStruct usage. -->
# Reference: JPA — Annotations, EntityManager and Data Access
## Basic JPA entity annotations
| Annotation | Use |
|-----------|-----|
| `@Entity` | Marks the class as a JPA entity |
| `@Table(name = "publishers")` | Table name — required when it differs from the class name |
| `@Id` | Primary key |
| `@GeneratedValue(strategy = GenerationType.IDENTITY)` | DB generates the ID automatically |
| `@GeneratedValue(strategy = GenerationType.AUTO)` | JPA chooses the strategy based on the engine |
| `@Column(name = "title_es")` | Column name when it differs from the Java attribute |
| `@Column(length = 2000)` | Maximum column length |
**Rule:** A JPA entity **MUST** have an empty constructor.
## Relationship annotations
| Annotation | Type | Use |
|-----------|------|-----|
| `@ManyToOne(fetch = FetchType.LAZY)` | Many-to-one | A book has one publisher |
| `@OneToMany(mappedBy = "book", cascade = CascadeType.ALL)` | One-to-many | A book has many authors |
| `@ManyToMany` | Many-to-many | Direct — discouraged; better to break it with an intermediate entity |
| `@JoinColumn(name = "publisher_id")` | FK in the table | Indicates the foreign key column |
| `@JoinTable(name = "books_authors", joinColumns = ..., inverseJoinColumns = ...)` | Intermediate table | For direct @ManyToMany |
**Recommendation:** Break `@ManyToMany` into two `@ManyToOne` with an intermediate entity (`BookAuthorJpaEntity`). Benefits: greater flexibility, better persistence control.
## EntityManager — operations
| Operation | Method | Description |
|-----------|--------|-------------|
| Create | `entityManager.persist(entity)` | Inserts into DB (on flush) |
| Read | `entityManager.find(BookJpaEntity.class, id)` | Retrieves by primary key |
| Update | `entityManager.merge(entity)` | Synchronises changes |
| Delete | `entityManager.remove(entity)` | Deletes the entity |
| JPQL | `entityManager.createQuery("SELECT b FROM BookJpaEntity b WHERE ...", ...)` | JPQL query |
| Flush | `entityManager.flush()` | Forces immediate write |
Injection:
```java
@PersistenceContext
private EntityManager entityManager;
```
## Data access types
### 1. JPQL — entity-oriented (recommended)
```java
List<BookJpaEntity> books = entityManager
    .createQuery("SELECT b FROM BookJpaEntity b WHERE b.author = :author", BookJpaEntity.class)
    .setParameter("author", "Isaac Asimov")
    .getResultList();
```
**Advantages:** readable, engine-independent, leverages relationships.
**Disadvantages:** does not support engine-specific functions.
### 2. Native SQL — raw SQL
```java
List<Object[]> result = entityManager
    .createNativeQuery("SELECT id, title FROM books WHERE author = ?1")
    .setParameter(1, "Isaac Asimov")
    .getResultList();
```
**Advantages:** full control, engine-specific functions.
**Disadvantages:** dialect-dependent, prone to maintenance errors.
### 3. Criteria API — programmatic and typed
```java
CriteriaBuilder cb = entityManager.getCriteriaBuilder();
CriteriaQuery<BookJpaEntity> cq = cb.createQuery(BookJpaEntity.class);
Root<BookJpaEntity> book = cq.from(BookJpaEntity.class);
cq.select(book).where(cb.equal(book.get("author"), "Isaac Asimov"));
List<BookJpaEntity> results = entityManager.createQuery(cq).getResultList();
```
**Advantages:** typed, compile-safe, ideal for dynamic filters.
**Disadvantages:** verbose, complex for simple queries.
## MapStruct — automatic mapping
```java
@Mapper
public interface PublisherMapper {
    PublisherMapper INSTANCE = Mappers.getMapper(PublisherMapper.class);
    PublisherJpaEntity toJpaEntity(PublisherEntity entity);
    PublisherEntity toDomainEntity(PublisherJpaEntity jpaEntity);
}
```
If fields have the same name and type, MapStruct generates the implementation automatically at compile time.
