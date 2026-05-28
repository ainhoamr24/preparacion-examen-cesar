<!-- Ejemplo del Tema 04. Descripción de la arquitectura limpia de Robert C. Martin: principio de independencia del núcleo respecto a frameworks, UI y BD, tabla de beneficios y violaciones típicas que hay que evitar. -->
# Ejemplo: Arquitectura Limpia (Robert C. Martin — Uncle Bob)
## Principio básico
El **núcleo del sistema** = lógica de negocio pura (entidades + casos de uso).
Los **detalles tecnológicos** (BD, frameworks, UI) son periféricos y deben poder cambiar sin afectar al núcleo.
## Beneficios
| Beneficio | Descripción |
|-----------|-------------|
| **Independencia del framework** | No depende de ningún framework específico; puede evolucionar |
| **Independencia de la UI** | Las interfaces pueden cambiar sin tocar la lógica central |
| **Independencia de la BD** | La lógica no está atada a ninguna base de datos concreta |
| **Facilidad de testing** | Los detalles externos están desacoplados → pruebas unitarias más sencillas |
## Violaciones típicas
- La capa de dominio importa clases de Spring (`@Service`, `@Autowired`) → **viola independencia del framework**.
- El servicio de dominio usa `EntityManager` directamente → **viola independencia de la BD**.
- El controlador contiene reglas de negocio (`if precio < 0 throw...`) → **viola separación de responsabilidades**.
