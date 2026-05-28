<!-- Referencia del Tema 05. Tabla de responsabilidades de cada capa indicando qué SÍ y qué NO contiene, y tablas de anotaciones Spring Boot agrupadas por capa (presentación: @RestController, @GetMapping…; dominio: @Service, @Transactional; persistencia: @Entity, @Repository…). -->
# Referencia: Capas — Responsabilidades y Reglas
## Tabla de responsabilidades
| Capa | Responsabilidad | SÍ contiene | NO contiene |
|------|----------------|-------------|-------------|
| **Presentación (controller)** | Recibir peticiones HTTP, validar entrada básica, delegar al servicio, devolver respuesta | Controladores, anotaciones `@RestController`, `@RequestMapping`, `@GetMapping`… | Lógica de negocio, acceso a repositorios |
| **Dominio** | Lógica de negocio, modelos, reglas del sistema | Modelos (entidades), servicios (interfaz + impl), interfaces de repositorio | Imports de Spring, JPA, o cualquier framework externo |
| **Persistencia** | Interacción con el origen de datos | Implementaciones de repositorio, DAOs, entidades JPA | Lógica de negocio |
## Anotaciones Spring Boot por capa
### Capa de presentación
| Anotación | Uso |
|-----------|-----|
| `@RestController` | Clase controladora REST; respuestas auto-serializadas a JSON |
| `@RequestMapping("/api/books")` | Mapea todas las rutas del controlador al prefijo indicado |
| `@GetMapping` / `@GetMapping("/{id}")` | Maneja GET; puede incluir path variable |
| `@PostMapping` | Maneja POST |
| `@PutMapping("/{id}")` | Maneja PUT |
| `@PatchMapping("/{id}")` | Maneja PATCH |
| `@DeleteMapping("/{id}")` | Maneja DELETE |
| `@PathVariable` | Extrae variable del path (`/books/{id}` → `Long id`) |
| `@RequestParam` | Extrae parámetro de query (`?page=3` → `int page`) |
| `@RequestBody` | Deserializa el cuerpo JSON de la petición |
| `@Valid` | Activa Bean Validation sobre el parámetro anotado |
### Capa de aplicación/dominio
| Anotación | Uso |
|-----------|-----|
| `@Service` | Marca la clase como servicio Spring (en la implementación) |
| `@Transactional` | Gestión automática de transacciones |
### Capa de persistencia
| Anotación | Uso |
|-----------|-----|
| `@Repository` | Marca la interfaz/clase como repositorio Spring |
| `@Entity` | Marca la clase como entidad JPA |
| `@Table(name="...")` | Nombre de la tabla en BD |
| `@Id` | Clave primaria |
| `@GeneratedValue(strategy=...)` | Generación automática del ID |
| `@Column(name="...")` | Nombre de la columna cuando difiere del atributo |
| `@ManyToOne`, `@OneToMany`, `@ManyToMany` | Relaciones entre entidades |
| `@PersistenceContext` | Inyecta el EntityManager |
