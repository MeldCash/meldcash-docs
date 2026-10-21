# Visión general de arquitectura

**Estado:** arquitectura **propuesta** para el MVP. No está implementada. No hay componentes reales en el repositorio.

## Estilo

Aplicación web adaptable (SPA) + API REST monolítica + PostgreSQL.

Usuarios humanos son el único actor externo confirmado. Integraciones externas **previstas, no confirmadas**: proveedor SMTP (Q-011, Q-017). No hay open banking.

## Contextos lógicos (módulos del monolito)

| Módulo | Responsabilidad | No hace |
| --- | --- | --- |
| Identity | Registro, sesión, perfil, reset | Autorización de negocio de grupos |
| Accounts | Cuentas personales, saldo cache | Gastos de grupo |
| Ledger | Movimientos personales, transferencias, categorías, anulaciones | Splits de grupo |
| Groups | Grupos, membresías, invitaciones, gastos compartidos, splits, liquidaciones, balances | Saldos bancarios personales |
| Planning | Presupuestos, metas, recurrentes | Cobros automáticos |
| Debts | Deudas personales y pagos | Balances de grupo |
| Reporting | Agregados e informes de un espacio | Mezclar espacios (DEC-007) |
| Export | CSV con los mismos filtros y auth que Reporting | Jobs asíncronos (DEC-011) |
| Audit | Append-only de cambios relevantes | PII innecesaria en logs |

Cada módulo expone facades de aplicación; la persistencia es compartida en **una** base con tablas por contexto.

## Límites de confianza

- El navegador no es de confianza: validación y autorización en API.
- La SPA solo muestra lo que la API autoriza.
- PostgreSQL solo acepta conexiones de la API (red interna).
- SMTP recibe el mínimo de PII (email, token de un uso).

## Autenticación y autorización (propuesto)

1. Login establece sesión.
2. Cada request lleva cookie; CSRF si cookie de sesión.
3. El `userId` de sesión es el único sujeto.
4. Recursos personales: `resource.user_id == session.userId`.
5. Recursos de grupo: existe `membership` ACTIVE para `(userId, groupId)` y, si Q-013 lo limita, reglas extra.
6. Administrador de grupo: flag o rol en membership (Q-006).

## Transacciones

Patrón: un caso de uso = una transacción de BD (`@Transactional`).

Ejemplos atómicos:

- Transferencia: dos piernas + cache de dos cuentas + idempotencia.
- Gasto compartido: cabecera + N splits.
- Liquidación: un settlement.
- Confirmación recurrente: ocurrencia + movimiento de ledger.

Aislamiento propuesto: `READ COMMITTED` + versionado optimista (`version`) en cuentas y gastos. Operaciones concurrentes sobre el mismo agregado: el segundo commit falla y el cliente reintenta.

## Manejo de errores

Ver [error-handling](../api/error-handling.md). La API no devuelve stack traces. Los errores de validación enumeran campos. Los 409 de versión indican conflicto.

## Flujos de datos principales

1. **Ingreso/gasto:** SPA → API Ledger → INSERT transaction → UPDATE account.cached_balance.
2. **Transferencia:** igual con dos transacciones enlazadas.
3. **Gasto compartido:** SPA → API Groups → INSERT expense + splits. No toca Ledger (Q-002).
4. **Balance:** lectura: agregar splits y settlements (o vista SQL). No es saldo de `accounts`.
5. **Liquidación:** INSERT settlement. Opcionalmente Ledger si se aprueba Q-004.
6. **Informe personal:** consultas al Ledger filtradas por usuario y período.
7. **Export:** misma consulta que listado, serializada a CSV, stream de respuesta.

## Integraciones externas previstas

| Sistema | Uso | Estado |
| --- | --- | --- |
| SMTP / proveedor email | Reset de clave, invitaciones | Pendiente Q-011/Q-017 |
| Navegador del usuario | UI | Confirmado por el brief (web) |
| Sistema bancario | — | Won't MVP |

## Entornos (propuestos)

- `local`: Compose.
- `piloto`: un API + un PostgreSQL + front estático + HTTPS.

No se define producción a escala.

## Lo que esta arquitectura no es

- No está desplegada.
- No hay alta disponibilidad, ni CQRS completo, ni event sourcing (un audit log no es event sourcing).
- No hay capa de “motor contable” de doble partida general: hay un modelo de hechos suficiente para personal + splits de grupo.
