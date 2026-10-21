# Decisiones de arquitectura

Registro estilo ADR. **Ninguna está aprobada** hasta que el propietario las firme. Estado: propuesto.

## DEC-001 — Stack del MVP

- **Contexto:** Repo vacío; tecnología no decidida.
- **Decisión propuesta:** React+TS / Spring Boot 3 Java 21 / PostgreSQL. Ver [evaluación](technology-evaluation.md).
- **Alternativas:** .NET, NestJS, Django.
- **Consecuencias:** Se necesita JDK, Node para el front, y PostgreSQL en local.

## DEC-002 — Monolito modular, no microservicios

- **Contexto:** Consistencia financiera entre entidades.
- **Decisión propuesta:** Un artefacto de API. Módulos internos por contexto.
- **Alternativas:** microservicios por dominio; BFF separado más API.
- **Consecuencias:** Despliegue simple; límites de módulo por paquete y reglas de dependencia, no por red.

## DEC-003 — Autenticación por cookie de sesión (o token opaco)

- **Contexto:** SPA + datos sensibles.
- **Decisión propuesta:** Sesión de servidor o token opaco en cookie `HttpOnly`/`Secure`/`SameSite`; CSRF si aplica.
- **Alternativas:** JWT en `localStorage` (rechazado: XSS); OAuth externo (Q-011).
- **Consecuencias:** API no es 100 % stateless; el balance es seguridad del navegador.

## DEC-004 — API REST versionada por prefijo

- **Decisión propuesta:** `/api/v1`.
- **Alternativas:** header de versión; GraphQL.
- **Consecuencias:** Contratos en `docs/api`. GraphQL no aporta al MVP y complica autorización por campo.

## DEC-005 — PostgreSQL como sistema de registro

- Importes `NUMERIC`. Sin réplicas complejas en MVP.
- **Alternativas:** ver evaluación.

## DEC-006 — Cuentas de titularidad individual en MVP

- Depende de Q-001.
- **Decisión propuesta:** `Account.user_id` obligatorio; no hay cuenta conjunta.
- **Consecuencias:** El dinero compartido vive en `shared_expense` + `settlement`, no en el saldo de un banco común.

## DEC-007 — Dos espacios de consulta, no un total mezclado

- Dashboard personal ≠ resumen de grupo.
- **Consecuencias:** Evita el anti-objetivo del brief. Una vista “todo mi dinero” queda Won't MVP.

## DEC-008 — Libro de hechos, saldos derivados

- **Decisión propuesta:** No persistir saldo denormalizado como fuente de verdad; calcular con RN-008 o mantener un *cache* de saldo actualizado **en la misma transacción** que el movimiento, con tests de igualdad. Recomendación: columna `cached_balance` en cuenta actualizada atómicamente **más** posibilidad de recomputar.
- **Alternativas:** solo derivado (simple, más coste de lectura); solo cache (riesgo de drift).
- **Consecuencias:** Transferencias y anulaciones actualizan cache + hechos.

## DEC-009 — Gastos compartidos como agregado propio

- No son `Transaction` con un flag `is_shared`.
- **Motivo:** RN-023 y prevención de duplicados.
- **Consecuencias:** Informes personales no los incluyen por defecto.

## DEC-010 — Anulación lógica, no DELETE físico de hechos

- Estados `VOID`.
- **Consecuencias:** Auditoría y recomputación.

## DEC-011 — Exportación síncrona CSV; sin cola

- **Consecuencias:** No hay entidad `ExportJob` en MVP. Si Q de volumen cambia, se introduce después.

## DEC-012 — Reloj y fechas

- `created_at`/`updated_at` en UTC; `value_date` tipo date. Q-015.

## DEC-013 — Idempotencia en escrituras financieras

- Header `Idempotency-Key` (UUID) con persistencia de respuesta.
- **Consecuencias:** Tabla `idempotency_record` (usuario, clave, método, path, hash cuerpo, status, body).

## Decisiones explícitamente no tomadas

- Proveedor cloud.
- Licencia del código.
- Si el front se sirve desde el mismo origen que la API (recomendación: mismo dominio vía reverse proxy para simplificar cookies).
