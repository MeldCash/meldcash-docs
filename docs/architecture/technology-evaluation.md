# Evaluación tecnológica

**Estado:** **propuesta**. Ningún stack está aprobado. El repositorio está vacío: no hay compatibilidad forzada con código legado.

Criterios (ponderación cualitativa para un MVP financiero, no un scoring inventado con decimales):

1. Complejidad acorde al MVP
2. Facilidad de aprendizaje y mantenimiento
3. Seguridad
4. Integridad de datos financieros (transacciones, decimal, constraints)
5. Coste de infraestructura
6. Calidad de herramientas de prueba
7. Escalabilidad razonable (piloto → miles de usuarios)
8. Documentación y comunidad
9. Compatibilidad con el repositorio actual (vacío)

No se recomiendan microservicios, Kubernetes ni bus de eventos: no hay necesidad demostrada.

## Frontend web adaptable

| Opción | Ventajas | Desventajas | Encaje MVP |
| --- | --- | --- | --- |
| **React + TypeScript** | Ecosistema amplio, componentes, testing (Testing Library), SPA contra API. | Hay que elegir routing, estado y empaquetado. | Alto |
| Angular | Opinión fuerte, buenas para equipos Java. | Más ceremonial para un MVP. | Medio |
| Vue 3 | Curva suave. | Menor coincidencia con muchos equipos Java. | Medio |
| Server-side (Thymeleaf, JSP) | Menos API. | Peor experiencia adaptable y de API documentada. | Bajo para este brief (API + web) |
| Next.js (React SSR) | SEO, un solo lenguaje si se unifica JS. | Complejidad extra; el brief pide API y no hay requisito de SEO de marketing. | Medio |

**Recomendación provisional:** React 18+ con TypeScript, Vite, React Router. Estado servidor vía fetch/TanStack Query. CSS: un sistema simple (p. ej. tokens propios o un kit accesible); no se impone librería visual.

## Backend y API

| Opción | Ventajas | Desventajas | Integridad financiera |
| --- | --- | --- | --- |
| **Java 21 + Spring Boot 3** | Transacciones, Bean Validation, Security, ecosistema maduro; el brief pide considerarlo. | Más verboso; requiere JDK. | Excelente |
| .NET 8 + ASP.NET Core | Similar solidez, `decimal`. | Si el equipo no es .NET, coste de aprendizaje. | Excelente |
| Node + NestJS | Un solo lenguaje con React. | Números JS son float nativos: hay que disciplinar decimal (`decimal.js` + NUMERIC en BD). | Buena si se cuida |
| Python + Django/DRF o FastAPI | Rapidez de desarrollo. | Decimal posible; tipado y empaquetado más débiles que Java/.NET para dominio financiero largo. | Buena |
| Go | Simple de operar. | Menos ecosistema de dominio; equipo desconocido. | Buena |

**Recomendación provisional:** **Java 21 + Spring Boot 3** (API REST monolítica modular). Motivos: transacciones declarativas, validación, seguridad, pruebas (`MockMvc`, Testcontainers), `BigDecimal` + PostgreSQL `NUMERIC`, y alineación con la alternativa que el brief pide evaluar. Alternativa equivalente si el equipo es .NET: ASP.NET Core.

Modularidad: paquetes por bounded context (`identity`, `accounts`, `ledger`, `groups`, `reporting`), **un** deployable.

## Persistencia

| Opción | Ventajas | Desventajas |
| --- | --- | --- |
| **PostgreSQL 16** | `NUMERIC`, constraints, TX ACID, índices, JSONB si hiciera falta, maduro. | Hay que operarlo (bajo coste en un contenedor). |
| MySQL 8 | Conocido. | `DECIMAL` existe; menor riqueza de constraints/CTE para algunos informes. |
| SQLite | Cero ops. | Concurrencia y tipo decimal más débiles para multi-usuario. |
| MongoDB | Flexibilidad. | Integridad financiera y splits relacionales peores; no recomendado como sistema de registro. |

**Recomendación:** PostgreSQL. Migraciones: Flyway o Liquibase. Acceso: Spring Data JDBC o JPA con cuidado de no perder control de TX; para el libro mayor se prefiere SQL explícito en agregados críticos.

## Autenticación y autorización

| Opción | Encaje |
| --- | --- |
| **Spring Security + sesión en cookie `HttpOnly` `Secure` `SameSite`** | Adecuado para SPA same-site o reverse proxy; CSRF token. |
| JWT access + refresh en cookie | Válido; más piezas (rotación, denylist). |
| OAuth2 Authorization Server propio | Exceso para MVP. |
| IdP externo (Auth0, Cognito, Keycloak) | Útil si no se quiere operar passwords; añade coste, proveedor y Q-011. |

**Recomendación:** Spring Security con sesión de servidor o token opaco en cookie; passwords con BCrypt/Argon2. Reset por email cuando Q-011 lo apruebe. Autorización: chequeos en servicio sobre `userId` y `groupId` (nunca solo “está autenticado”).

## Validación

- API: Bean Validation (`jakarta.validation`) + reglas de dominio en servicios.
- Frontend: validación UX; **la fuente de verdad es el servidor**.
- BD: `CHECK`, `NOT NULL`, `UNIQUE`, FK.

## Documentación de API

**springdoc-openapi** (OpenAPI 3) generado del código cuando exista. Hasta entonces, los contratos de `docs/api/` son la especificación de diseño.

## Pruebas automatizadas

| Capa | Herramienta propuesta |
| --- | --- |
| Unidad Java | JUnit 5, AssertJ |
| Integración BD | Testcontainers + PostgreSQL |
| API | MockMvc o REST Assured |
| Frontend | Vitest + Testing Library |
| E2E | Playwright (flujos Must) |
| Propiedades financieras | tests parametrizados de redondeo y balances |

## Despliegue

| Opción | Comentario |
| --- | --- |
| **Docker Compose** (SPA estática + API + PostgreSQL) | Suficiente para piloto. |
| PaaS (Fly.io, Render, Railway, Azure App, Cloud Run) | Un servicio API + un PostgreSQL gestionado + hosting estático. Elegir **después** de coste y región (Q-018). |
| Kubernetes | No para MVP. |

CI propuesta: GitHub Actions (el remoto ya es GitHub): lint, tests, build. No hay pipeline hoy.

## Observabilidad

MVP: logs estructurados JSON (Logback) + correlation id + health/liveness. Métricas Prometheus: Should. APM: Could.

## Exportaciones

| Formato | Biblioteca (Java) | MoSCoW |
| --- | --- | --- |
| CSV | OpenCSV o writer propio con escape RFC 4180 | Must |
| Excel | Apache POI | Could |
| PDF | OpenPDF | Could |

Generación **síncrona** para volúmenes de piloto.

## Recomendación principal (DEC-001, propuesta)

| Pieza | Elección propuesta |
| --- | --- |
| Frontend | React + TypeScript + Vite |
| Backend | Java 21, Spring Boot 3, API REST monolítica modular |
| BD | PostgreSQL 16, `NUMERIC`, Flyway |
| Auth | Spring Security, cookie segura, Argon2/BCrypt |
| API docs | OpenAPI 3 / springdoc |
| Tests | JUnit, Testcontainers, Vitest, Playwright |
| Local | Docker Compose |
| Export | CSV (OpenCSV) |

**Por qué no Node “porque el front es JS”:** el dominio es un libro de hechos económicos con consistencia; Java + PostgreSQL reducen el riesgo de decimal y TX. El repo vacío no obliga a unificar lenguaje.

**Por qué no microservicios:** un equipo aún no definido, un MVP, transacciones que cruzarían servicios (gasto + splits).

## Coste

Sin tarifas concretas: el piloto cabe en un único nodo pequeño + PostgreSQL gestionado de entrada de gama. El coste dominante será tiempo de desarrollo, no infra.

## Dependencia de equipo

Si el equipo no tiene Java, DEC-001 debe reabrirse hacia ASP.NET o NestJS **antes** de escribir código. Esta evaluación asume que Java es aceptable, no que esté confirmado.
