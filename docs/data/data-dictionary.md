# Diccionario de datos

**Estado:** propuesto. Tipos PostgreSQL. Campos de auditoría comunes omitidos en tablas salvo que cambien el significado: `created_at timestamptz NOT NULL`, `updated_at timestamptz NOT NULL`.

Convenciones: UUID v4; fechas de valor `date`; importes `numeric(19,2)`.

## user

| Columna | Tipo | Nulo | Descripción |
| --- | --- | --- | --- |
| id | uuid | no | PK |
| email | citext o varchar(320) | no | UNIQUE, login |
| password_hash | varchar | no | Nunca se expone |
| display_name | varchar(100) | no | Visible en grupos |
| status | varchar(20) | no | ACTIVE, DISABLED |
| preferred_currency | char(3) | sí | Q-007 |
| time_zone | varchar(64) | sí | Q-015 |

## account

| Columna | Tipo | Nulo | Descripción |
| --- | --- | --- | --- |
| id | uuid | no | PK |
| user_id | uuid | no | FK user |
| name | varchar(80) | no | |
| type | varchar(20) | no | CASH, BANK |
| currency_code | char(3) | no | ISO 4217 |
| initial_balance | numeric(19,2) | no | Puede ser 0 |
| cached_balance | numeric(19,2) | no | RN-008 |
| status | varchar(20) | no | ACTIVE, ARCHIVED |
| version | bigint | no | Optimistic lock |

## category

| Columna | Tipo | Nulo | Descripción |
| --- | --- | --- | --- |
| id | uuid | no | PK |
| user_id | uuid | sí | NULL = sistema |
| name | varchar(80) | no | |
| kind | varchar(20) | no | INCOME, EXPENSE, BOTH |
| archived | boolean | no | default false |

## transaction

| Columna | Tipo | Nulo | Descripción |
| --- | --- | --- | --- |
| id | uuid | no | PK |
| user_id | uuid | no | Dueño |
| account_id | uuid | no | FK account |
| category_id | uuid | sí | Q-024 |
| type | varchar(20) | no | INCOME, EXPENSE, TRANSFER_IN, TRANSFER_OUT |
| amount | numeric(19,2) | no | CHECK (amount > 0) |
| value_date | date | no | Fecha contable |
| description | varchar(280) | sí | |
| transfer_id | uuid | sí | Enlaza par de transferencias |
| recurring_occurrence_id | uuid | sí | |
| status | varchar(20) | no | POSTED, VOID |
| voided_at | timestamptz | sí | |
| voided_by | uuid | sí | |
| version | bigint | no | |

## group

| Columna | Tipo | Nulo | Descripción |
| --- | --- | --- | --- |
| id | uuid | no | PK |
| name | varchar(80) | no | |
| currency_code | char(3) | no | Moneda del grupo |
| created_by | uuid | no | FK user |
| status | varchar(20) | no | ACTIVE, ARCHIVED |

## group_membership

| Columna | Tipo | Nulo | Descripción |
| --- | --- | --- | --- |
| id | uuid | no | PK |
| group_id | uuid | no | FK |
| user_id | uuid | no | FK |
| role | varchar(20) | no | ADMIN, MEMBER (propuesto) |
| status | varchar(20) | no | INVITED, ACTIVE, LEFT |
| joined_at | timestamptz | sí | |
| left_at | timestamptz | sí | |

UNIQUE (group_id, user_id).

## group_invitation

| Columna | Tipo | Nulo | Descripción |
| --- | --- | --- | --- |
| id | uuid | no | PK |
| group_id | uuid | no | |
| email | varchar(320) | no | Destinatario |
| token_hash | varchar | no | |
| expires_at | timestamptz | no | Q-017 |
| accepted_at | timestamptz | sí | |
| created_by | uuid | no | |

## shared_expense

| Columna | Tipo | Nulo | Descripción |
| --- | --- | --- | --- |
| id | uuid | no | PK |
| group_id | uuid | no | |
| payer_user_id | uuid | no | Quién pagó |
| amount | numeric(19,2) | no | CHECK > 0 |
| currency_code | char(3) | no | = group |
| value_date | date | no | |
| description | varchar(280) | sí | |
| split_mode | varchar(20) | no | EQUAL, EXACT |
| status | varchar(20) | no | POSTED, VOID |
| version | bigint | no | |

## shared_expense_split

| Columna | Tipo | Nulo | Descripción |
| --- | --- | --- | --- |
| id | uuid | no | PK |
| shared_expense_id | uuid | no | ON DELETE no físico; void en cabecera |
| participant_user_id | uuid | no | |
| amount | numeric(19,2) | no | CHECK > 0 |

UNIQUE (shared_expense_id, participant_user_id).

## settlement

| Columna | Tipo | Nulo | Descripción |
| --- | --- | --- | --- |
| id | uuid | no | PK |
| group_id | uuid | no | |
| from_user_id | uuid | no | Quien paga la liquidación |
| to_user_id | uuid | no | Quien recibe |
| amount | numeric(19,2) | no | CHECK > 0 |
| currency_code | char(3) | no | |
| value_date | date | no | |
| note | varchar(280) | sí | |
| status | varchar(20) | no | POSTED, VOID |

CHECK (from_user_id <> to_user_id).

## budget

| Columna | Tipo | Nulo | Descripción |
| --- | --- | --- | --- |
| id | uuid | no | PK |
| user_id | uuid | no | |
| category_id | uuid | no | |
| period_start | date | no | Inclusivo |
| period_end | date | no | Inclusivo |
| amount | numeric(19,2) | no | Límite |
| currency_code | char(3) | no | |

CHECK (period_end >= period_start).

## debt

| Columna | Tipo | Nulo | Descripción |
| --- | --- | --- | --- |
| id | uuid | no | PK |
| user_id | uuid | no | Dueño del registro |
| counterparty_name | varchar(120) | no | Q-023 |
| direction | varchar(20) | no | I_OWE, OWED_TO_ME |
| original_amount | numeric(19,2) | no | |
| currency_code | char(3) | no | |
| due_date | date | sí | |
| status | varchar(20) | no | OPEN, SETTLED, VOID |

El pendiente no se almacena como fuente de verdad; se deriva (RN-045). Puede cachearse con la misma precaución que cuentas.

## debt_payment

| Columna | Tipo | Nulo | Descripción |
| --- | --- | --- | --- |
| id | uuid | no | PK |
| debt_id | uuid | no | |
| amount | numeric(19,2) | no | |
| value_date | date | no | |
| transaction_id | uuid | sí | Q-025 |
| status | varchar(20) | no | POSTED, VOID |

## recurring_rule

| Columna | Tipo | Nulo | Descripción |
| --- | --- | --- | --- |
| id | uuid | no | PK |
| user_id | uuid | no | |
| account_id | uuid | no | |
| category_id | uuid | sí | |
| type | varchar(20) | no | INCOME, EXPENSE |
| amount | numeric(19,2) | no | Referencia, no cobro |
| frequency | varchar(20) | no | WEEKLY, MONTHLY |
| next_date | date | no | |
| status | varchar(20) | no | ACTIVE, CANCELLED |

## recurring_occurrence

| Columna | Tipo | Nulo | Descripción |
| --- | --- | --- | --- |
| id | uuid | no | PK |
| rule_id | uuid | no | |
| due_date | date | no | |
| status | varchar(20) | no | PENDING, PAID, SKIPPED |
| transaction_id | uuid | sí | UNIQUE si no nulo |

## idempotency_record

| Columna | Tipo | Nulo | Descripción |
| --- | --- | --- | --- |
| id | uuid | no | PK |
| user_id | uuid | no | |
| key | varchar(80) | no | Header |
| method | varchar(10) | no | |
| path | varchar(200) | no | |
| request_hash | varchar(64) | no | |
| response_code | int | no | |
| response_body | jsonb | no | Recorte de tamaño pendiente |
| created_at | timestamptz | no | |

UNIQUE (user_id, key).

## audit_event

| Columna | Tipo | Nulo | Descripción |
| --- | --- | --- | --- |
| id | uuid | no | PK |
| actor_user_id | uuid | sí | Sistema si nulo |
| entity_type | varchar(40) | no | |
| entity_id | uuid | no | |
| action | varchar(40) | no | CREATE, UPDATE, VOID |
| at | timestamptz | no | |
| payload_json | jsonb | sí | Sin secretos |

## Índices adicionales justificados

| Índice | Motivo |
| --- | --- |
| transaction (user_id, value_date DESC) | Listados e informes |
| transaction (account_id, status) | Recálculo de saldo |
| shared_expense (group_id, value_date) | Historial de grupo |
| settlement (group_id, value_date) | Balances |
| group_membership (user_id, status) | “Mis grupos” |
