<!-- Ejemplo del Tema 07. Implementación completa de la capa de persistencia: entidad JPA con relaciones @ManyToOne y @OneToMany, DAO JPA con EntityManager (count, findByIsbn, insert, update con flush), y repositorio que implementa la interfaz de dominio usando el DAO y el mapper. -->
# Ejemplo completo: DAO y Repositorio JPA
## Entidad JPA
```java
@Entity
@Table(name = "books")
public class BookJpaEntity implements Serializable {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String isbn;
    @Column(name = "title_es")
    private String titleEs;
    @Column(name = "base_price")
    private BigDecimal basePrice;
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "publisher_id")
    private PublisherJpaEntity publisher;
    @OneToMany(mappedBy = "book", cascade = CascadeType.ALL)
    private List<BookAuthorJpaEntity> bookAuthors = new ArrayList<>();
    // Constructor vacío OBLIGATORIO para JPA
    public BookJpaEntity() {}
    // getters, setters
}
```
## DAO JPA
```java
public class BookJpaDaoImpl implements BookJpaDao {
    @PersistenceContext
    private EntityManager entityManager;
    @Override
    public long count() {
        return entityManager.createQuery("SELECT COUNT(b) FROM BookJpaEntity b", Long.class)
            .getSingleResult();
    }
    @Override
    public Optional<BookJpaEntity> findByIsbn(String isbn) {
        try {
            return Optional.of(
                entityManager.createQuery(
                    "SELECT b FROM BookJpaEntity b WHERE b.isbn = :isbn", BookJpaEntity.class)
                    .setParameter("isbn", isbn)
                    .getSingleResult()
            );
        } catch (Exception e) {
            return Optional.empty();
        }
    }
    @Override
    public BookJpaEntity insert(BookJpaEntity entity) {
        entityManager.persist(entity);
        return entity;
    }
    @Override
    public BookJpaEntity update(BookJpaEntity entity) {
        BookJpaEntity managed = entityManager.find(BookJpaEntity.class, entity.getId());
        managed.getBookAuthors().clear();
        entityManager.flush(); // borrar autores antes de volver a insertar
        return entityManager.merge(entity);
    }
}
```
## Repositorio
```java
public class BookRepositoryImpl implements BookRepository {
    private final BookJpaDao bookJpaDao;
    public BookRepositoryImpl(BookJpaDao bookJpaDao) {
        this.bookJpaDao = bookJpaDao;
    }
    @Override
    public Optional<BookEntity> findByIsbn(String isbn) {
        return bookJpaDao.findByIsbn(isbn)
            .map(BookMapper.INSTANCE::fromBookJpaEntityToBookEntity);
    }
    @Override
    public BookEntity save(BookEntity bookEntity) {
        BookJpaEntity jpa = BookMapper.INSTANCE.fromBookEntityToBookJpaEntity(bookEntity);
        if (bookEntity.id() == null) {
            return BookMapper.INSTANCE.fromBookJpaEntityToBookEntity(bookJpaDao.insert(jpa));
        }
        return BookMapper.INSTANCE.fromBookJpaEntityToBookEntity(bookJpaDao.update(jpa));
    }
}
```
