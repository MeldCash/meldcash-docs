# Visión general de la API

**Estado:** contrato **propuesto**. No hay endpoints implementados.

## Convenciones

| Tema | Propuesta |
| --- | --- |
| Estilo | REST, JSON UTF-8 |
| Prefijo | `/api/v1` |
| Recursos | Sustantivos en plural, kebab-case |
| IDs | UUID en path |
| Fechas | `valueDate` como `YYYY-MM-DD`; instantes `ISO-8601` UTC |
| Importes | string decimal `"90.00"` **o** number JSON — **recomendación: string** para no perder precisión en clientes JS. Pendiente de aprobación. |
| Moneda | `currency` ISO 4217 |
| Paginación | `page` (0-based), `size` (default 20, max 100), respuesta `{ items, page, size, totalItems }` |
| Orden | `sort=valueDate,desc` |
| Filtros | query params `from`, `to`, `accountId`, `categoryId`, `type`, `q` |
| Idioma | nombres de campos en inglés (código); mensajes de error pueden ir en español (Q-027) |
| Idempotencia | header `Idempotency-Key` obligatorio en POST financieros |
| Concurrencia | header `If-Match` con `version` en PATCH/VOID |
| Auth | cookie de sesión; no se documentan nombres de cookie reales de producción aquí de forma vinculante |

## Autenticación

- `POST /api/v1/auth/register`
- `POST /api/v1/auth/login`
- `POST /api/v1/auth/logout`
- `POST /api/v1/auth/password-reset` (Should, Q-011)
- `GET /api/v1/me`

Las mutaciones de autenticación no usan Idempotency-Key de ledger, pero login debe limitar intentos (seguridad).

## Mapa de recursos (Must / Should)

| Recurso | Must |
| --- | --- |
| `/me` | sí |
| `/accounts` | sí |
| `/categories` | sí |
| `/transactions` | sí |
| `/transfers` | sí |
| `/reports/personal-summary` | sí |
| `/groups` | sí |
| `/groups/{id}/members` | sí |
| `/groups/{id}/invitations` | sí |
| `/groups/{id}/shared-expenses` | sí |
| `/groups/{id}/settlements` | sí |
| `/groups/{id}/balances` | sí |
| `/exports/personal-transactions` | sí |
| `/budgets` | Should |
| `/debts` | Should |
| `/recurring-rules` | Should |
| `/exports/group/{id}` | Should |

## Permisos (resumen)

| Operación | Quién |
| --- | --- |
| CRUD cuentas y ledger | dueño |
| Crear grupo | usuario autenticado |
| Invitar | ADMIN (propuesto, Q-006) |
| Crear gasto/liquidación | MEMBER ACTIVE |
| Ver balances | MEMBER ACTIVE (Q-013) |
| Export grupo | MEMBER ACTIVE si se aprueba RF-081 |

403 si autenticado pero sin acceso; 404 opcional para no revelar existencia de IDs ajenos (**recomendación:** 404 en recursos de otros usuarios para reducir enumeración).

## Versionado

Cambios incompatibles → `/api/v2`. Cambios compatibles (campos nuevos opcionales) en v1.

## OpenAPI

Cuando exista código, springdoc publicará `/v3/api-docs`. Hasta entonces este directorio es la fuente.
