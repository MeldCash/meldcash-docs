# Matriz de trazabilidad

**Estado:** propuesta. Solo requisitos **Must** (y Should citados en escenarios) para mantener la matriz útil.

| RF | RN | CU | US | Módulo | Entidades | API | Pruebas |
| --- | --- | --- | --- | --- | --- | --- | --- |
| RF-001, RF-002 | — | CU-001 | US-001 | Identity | user | `/auth/*`, `/me` | Login 401, registro |
| RF-010, RF-011 | RN-008, RN-010 | — | US-010 | Accounts | account | `/accounts` | TS-011 |
| RF-020 | RN-002, RN-003, RN-008, RN-020 | CU-010 | US-020 | Ledger | transaction | `/transactions` | TS-001 |
| RF-021 | RN-021 | CU-010 | US-021 | Ledger | transaction | `/transactions` | TS-002 |
| RF-022 | RN-014, RN-022 | CU-011 | US-022 | Ledger | transaction | `/transfers` | TS-003 |
| RF-023, RF-024 | RN-012, RN-013 | CU-012 | US-023 | Ledger | transaction, audit_event | void/patch | TS-012 |
| RF-025 | — | — | — | Ledger | — | GET filtros | — |
| RF-027 | Q-020, Q-024 | — | — | Ledger | category | `/categories` | — |
| RF-040, RF-042, RF-041 | RN-020–RN-024 | CU-020 | US-040 | Reporting | transaction, account | `/reports/personal-summary` | TS-007 |
| RF-045, RF-037 | RN-038 | CU-034 | US-035 | Groups | shared_expense, settlement | `/balances` | TS-006 |
| RF-030, RF-031, RF-032 | RN-051 | CU-030, CU-031 | US-030 | Groups | group, membership, invitation | `/groups` | membresía |
| RF-034, RF-035, RF-036 | RN-034–RN-037, RN-043 | CU-032 | US-031–033 | Groups | shared_expense, split | `/shared-expenses` | TS-004, TS-005 |
| RF-038 | RN-039, RN-041 | CU-033 | US-034 | Groups | settlement | `/settlements` | TS-006 |
| RF-080 | RN-052, RNF-013 | CU-060 | US-080 | Export | — | `/exports/personal-transactions` | TS-010 |
| RF-050, RF-051 | RN-030–RN-033 | CU-040 | US-050 | Planning | budget | `/budgets` | TS-008 |
| RF-060, RF-061 | RN-045–RN-047 | CU-070 | US-060 | Debts | debt, debt_payment | `/debts` | unitario overpay |
| RF-070–RF-073 | RN-040, RN-044 | CU-050 | US-070 | Planning | recurring_* | `/recurring-*` | TS-009 |

## NFR transversales

| RNF | Dónde se verifica |
| --- | --- |
| RNF-001 | Integración transferencia y shared-expense |
| RNF-002 | Revisión de tipos NUMERIC/BigDecimal; test que falle si alguien usa double |
| RNF-003 | TS-003 idempotencia |
| RNF-004 | Test de concurrencia de cuenta |
| RNF-010–RNF-013 | Seguridad + TS-010 + TS-011 |
| RNF-020 | Playwright viewports |

## Decisiones ligadas

| DEC | RF / RN |
| --- | --- |
| DEC-001 | todos |
| DEC-006 | RF-010, RN-010, Q-001 |
| DEC-007 | RF-040, RF-045, RN-023 |
| DEC-009 | RF-034 |
| DEC-011 | RF-080 |
| DEC-013 | RNF-003 |

## Preguntas que cortan trazos

Si Q-002 se resuelve por copiar al ledger, hay que añadir RF de “reflejo opcional”, entidad `transaction.shared_expense_id` y nuevos tests de no doble conteo en informes.
