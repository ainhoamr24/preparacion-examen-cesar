<!-- Reference for Topic 05. Responsibility table for each layer indicating what IS and IS NOT included, and Spring Boot annotation tables grouped by layer (presentation: @RestController, @GetMapping…; domain: @Service, @Transactional; persistence: @Entity, @Repository…). -->
# Reference: Layers — Responsibilities and Rules
## Responsibility table
| Layer | Responsibility | DOES contain | DOES NOT contain |
|-------|---------------|-------------|-----------------|
| **Presentation (controller)** | Receive HTTP requests, validate basic input, delegate to service, return response | Controllers, `@RestController`, `@RequestMapping`, `@GetMapping`… annotations | Business logic, repository access |
| **Domain** | Business logic, models, system rules | Models (entities), services (interface + impl), repository interfaces | Spring, JPA or any external framework imports |
| **Persistence** | Interaction with the data source | Repository implementations, DAOs, JPA entities | Business logic |
## Spring Boot annotations by layer
### Presentation layer
| Annotation | Use |
|-----------|-----|
| `@RestController` | REST controller class; responses auto-serialised to JSON |
| `@RequestMapping("/api/books")` | Maps all controller routes to the given prefix |
| `@GetMapping` / `@GetMapping("/{id}")` | Handles GET; can include a path variable |
| `@PostMapping` | Handles POST |
| `@PutMapping("/{id}")` | Handles PUT |
| `@PatchMapping("/{id}")` | Handles PATCH |
| `@DeleteMapping("/{id}")` | Handles DELETE |
| `@PathVariable` | Extracts variable from path (`/books/{id}` → `Long id`) |
| `@RequestParam` | Extracts query parameter (`?page=3` → `int page`) |
| `@RequestBody` | Deserialises the JSON body of the request |
| `@Valid` | Activates Bean Validation on the annotated parameter |
### Application/domain layer
| Annotation | Use |
|-----------|-----|
| `@Service` | Marks the class as a Spring service (on the implementation) |
| `@Transactional` | Automatic transaction management |
### Persistence layer
| Annotation | Use |
|-----------|-----|
| `@Repository` | Marks the interface/class as a Spring repository |
| `@Entity` | Marks the class as a JPA entity |
| `@Table(name="...")` | Table name in the DB |
| `@Id` | Primary key |
| `@GeneratedValue(strategy=...)` | Automatic ID generation |
| `@Column(name="...")` | Column name when it differs from the attribute |
| `@ManyToOne`, `@OneToMany`, `@ManyToMany` | Relationships between entities |
| `@PersistenceContext` | Injects the EntityManager |
