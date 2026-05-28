---
name: actions-reviewer
description: Criterios de revisión de código Java/Spring Boot del proyecto EXAMEN-CESAR. Define qué es BLOCKING (viola arquitectura por capas, mal código de estado HTTP, lógica de negocio en controlador, código que no compila), IMPORTANT (incumple convenciones REST o Spring Boot, tests ausentes) y MINOR (mejora de calidad menor). Cárgalo siempre que revises código del proyecto.
---
# actions-reviewer
Criterios de revisión de código para EXAMEN-CESAR. Cada problema encontrado **MUST** clasificarse como BLOCKING, IMPORTANT o MINOR según estas reglas.
---
## BLOCKING — Impide aprobar
Problemas que rompen la funcionalidad, la arquitectura o producen código que no compila.
### Arquitectura y estructura
- Un controlador inyecta una clase de `persistence` directamente.
- Un servicio recibe objetos HTTP (`HttpServletRequest`, `Model`, `HttpServletResponse`).
- Lógica de negocio (`if/else` de negocio, cálculos) implementada en el controlador.
- Se documenta o implementa una estructura de paquetes distinta a la adoptada en el proyecto.
### Diseño REST
- Verbos en la URL: `/getBooks`, `/createUser`, `/deleteItem`.
- Códigos de estado semánticamente incorrectos: `POST` que devuelve `200` en vez de `201`, `DELETE` que devuelve `200` en vez de `204`.
- Endpoint que no empieza por `/api`.
- Respuesta que no es JSON.
### Spring Boot
- Excepciones de negocio capturadas en el controlador.
- Un servicio devuelve una entidad JPA directamente al controlador (no un DTO).
- Código que no compila (import inexistente, método no encontrado, tipo incompatible).
---
## IMPORTANT — Incumple convenciones del proyecto
Problemas que incumplen convenciones establecidas en el CLAUDE.md pero no rompen la funcionalidad inmediatamente.
### Spring Boot
- `@Autowired` en campo en vez de inyección por constructor.
- `@Transactional` en el controlador en vez de en el servicio.
- Falta `@Valid` en parámetros de entrada que llevan anotaciones de validación.
- El servicio no tiene interfaz separada de la implementación cuando la convención del módulo la exige.
### Testing
- Faltan tests unitarios del servicio para la funcionalidad implementada.
- Tests que dependen del orden de ejecución.
- No se usa JUnit 5 (`@Test`, `@ExtendWith`) o no se usa AssertJ para aserciones.
### Nomenclatura
- Clases que no siguen `PascalCase`.
- Métodos o variables que no siguen `camelCase`.
- Endpoints en `camelCase` en vez de `kebab-case`.
- Recurso en singular en la URL.
---
## MINOR — Mejora de calidad
Problemas menores que no incumplen convenciones críticas pero mejoran la calidad.
- Imports sin usar.
- Métodos privados sin usar (código muerto).
- Falta `@DisplayName` en tests.
- Uso de `assertEquals` en vez de `assertThat(...).isEqualTo(...)` de AssertJ.
- Comentarios innecesarios o desactualizados.
- Mezcla de `when/thenReturn` y `given/willReturn` de Mockito en el mismo test.
---
## Checklist de revisión completo
Antes de emitir el veredicto, verifica cada punto:
**Arquitectura:**
- [ ] ¿El controlador solo delega al servicio sin lógica de negocio?
- [ ] ¿El controlador no inyecta clases de `persistence`?
- [ ] ¿El servicio no recibe objetos HTTP?
- [ ] ¿Las entidades JPA no se exponen directamente en la respuesta del endpoint?
**REST:**
- [ ] ¿Los endpoints usan sustantivos en plural?
- [ ] ¿Los endpoints empiezan por `/api`?
- [ ] ¿`POST` devuelve `201`? ¿`DELETE` devuelve `204`? ¿`GET`/`PUT`/`PATCH` devuelven `200`?
- [ ] ¿Las respuestas son JSON?
**Spring Boot:**
- [ ] ¿Inyección por constructor en todos los beans?
- [ ] ¿`@Valid` en parámetros de entrada que lo necesitan?
- [ ] ¿Las excepciones de negocio no se capturan en el controlador?
- [ ] ¿Los servicios siguen la convención interfaz/implementación del módulo?
**Testing:**
- [ ] ¿Hay tests unitarios del servicio?
- [ ] ¿Se usa JUnit 5 y AssertJ?
- [ ] ¿Los tests siguen el patrón Given/When/Then?
**Código:**
- [ ] ¿El código compila sin errores?
- [ ] ¿No hay imports muertos ni código muerto?
