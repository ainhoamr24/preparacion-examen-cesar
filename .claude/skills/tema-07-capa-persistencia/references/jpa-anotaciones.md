<!-- Referencia del Tema 07. Tablas de anotaciones JPA básicas (@Entity, @Id, @Column…) y de relaciones (@ManyToOne, @OneToMany, @ManyToMany con recomendación de romperla), operaciones del EntityManager (persist, merge, remove, flush), los tres métodos de consulta con ejemplos (JPQL, Native SQL, Criteria API) y uso de MapStruct. -->
# Referencia: JPA — Anotaciones, EntityManager y Acceso a Datos
## Anotaciones básicas de entidad JPA
| Anotación | Uso |
|-----------|-----|
| `@Entity` | Marca la clase como entidad JPA |
| `@Table(name = "publishers")` | Nombre de la tabla — obligatorio si difiere del nombre de la clase |
| `@Id` | Clave primaria |
| `@GeneratedValue(strategy = GenerationType.IDENTITY)` | BD genera el ID automáticamente |
| `@GeneratedValue(strategy = GenerationType.AUTO)` | JPA elige la estrategia según el motor |
| `@Column(name = "title_es")` | Nombre de columna cuando difiere del atributo Java |
| `@Column(length = 2000)` | Longitud máxima de la columna |
**Regla:** Una entidad JPA **MUST** tener un constructor vacío.
## Anotaciones de relaciones
| Anotación | Tipo | Uso |
|-----------|------|-----|
| `@ManyToOne(fetch = FetchType.LAZY)` | Muchos a uno | Un libro tiene un publisher |
| `@OneToMany(mappedBy = "book", cascade = CascadeType.ALL)` | Uno a muchos | Un libro tiene muchos autores |
| `@ManyToMany` | Muchos a muchos | Directo — se desaconseja; mejor romperlo con entidad intermedia |
| `@JoinColumn(name = "publisher_id")` | FK en la tabla | Indica la columna de la clave foránea |
| `@JoinTable(name = "books_authors", joinColumns = ..., inverseJoinColumns = ...)` | Tabla intermedia | Para @ManyToMany directo |
**Recomendación:** Romper `@ManyToMany` en dos `@ManyToOne` con una entidad intermedia (`BookAuthorJpaEntity`). Ventajas: mayor flexibilidad, mejor control de persistencia.
## EntityManager — operaciones
| Operación | Método | Descripción |
|-----------|--------|-------------|
| Crear | `entityManager.persist(entity)` | Inserta en BD (al sincronizar) |
| Leer | `entityManager.find(BookJpaEntity.class, id)` | Recupera por clave primaria |
| Actualizar | `entityManager.merge(entity)` | Sincroniza cambios |
| Borrar | `entityManager.remove(entity)` | Elimina la entidad |
| JPQL | `entityManager.createQuery("SELECT b FROM BookJpaEntity b WHERE ...", ...)` | Consulta JPQL |
| Sincronizar | `entityManager.flush()` | Fuerza escritura inmediata |
Inyección:
```java
@PersistenceContext
private EntityManager entityManager;
```
## Tipos de acceso a datos
### 1. JPQL — orientado a entidades (recomendado)
```java
List<BookJpaEntity> books = entityManager
    .createQuery("SELECT b FROM BookJpaEntity b WHERE b.author = :author", BookJpaEntity.class)
    .setParameter("author", "Isaac Asimov")
    .getResultList();
```
**Ventajas:** legible, independiente del motor, aprovecha relaciones.
**Inconvenientes:** no soporta funciones específicas de cada motor SQL.
### 2. Native SQL — SQL crudo
```java
List<Object[]> result = entityManager
    .createNativeQuery("SELECT id, title FROM books WHERE author = ?1")
    .setParameter(1, "Isaac Asimov")
    .getResultList();
```
**Ventajas:** control total, funciones específicas del motor.
**Inconvenientes:** dependiente del dialecto, propenso a errores de mantenimiento.
### 3. Criteria API — programático y tipado
```java
CriteriaBuilder cb = entityManager.getCriteriaBuilder();
CriteriaQuery<BookJpaEntity> cq = cb.createQuery(BookJpaEntity.class);
Root<BookJpaEntity> book = cq.from(BookJpaEntity.class);
cq.select(book).where(cb.equal(book.get("author"), "Isaac Asimov"));
List<BookJpaEntity> results = entityManager.createQuery(cq).getResultList();
```
**Ventajas:** tipado, seguro en compilación, ideal para filtros dinámicos.
**Inconvenientes:** verboso, complejo para consultas simples.
## MapStruct — mapeo automático
```java
@Mapper
public interface PublisherMapper {
    PublisherMapper INSTANCE = Mappers.getMapper(PublisherMapper.class);
    PublisherJpaEntity toJpaEntity(PublisherEntity entity);
    PublisherEntity toDomainEntity(PublisherJpaEntity jpaEntity);
}
```
Si los campos tienen el mismo nombre y tipo, MapStruct genera la implementación automáticamente en tiempo de compilación.
