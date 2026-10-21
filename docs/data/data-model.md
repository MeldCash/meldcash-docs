# Modelo de datos

**Estado:** modelo **conceptual y lógico propuesto**. No es un esquema ejecutado ni una migración.

Principios:

1. Importes: `NUMERIC(19,2)` (escala sujeta a Q-007). Nunca `FLOAT`/`DOUBLE`/`REAL`.
2. Claves: UUID (`uuid`) como PK de aplicación.
3. Auditoría: `created_at`, `updated_at` timestamptz; tablas de hechos con `created_by`.
4. Borrado: `status` / `voided_at`, no DELETE de hechos.
5. Una moneda por cuenta y por grupo (`CHAR(3)` ISO 4217).
6. Saldos de grupo **no** se guardan como tabla de verdad; se derivan. Opcional: materializar después.

No todas las entidades candidatas del brief se usan: `ExportJob` se omite (DEC-011). `TransactionSplit` se usa solo en gastos compartidos, no en el ledger personal (las transferencias usan dos filas `transaction` enlazadas).

## Entidades propuestas

### user

- **Propósito:** identidad de persona.
- **PK:** `id` UUID
- **Atributos:** `email` UNIQUE NOT NULL, `password_hash` NOT NULL, `display_name` NOT NULL, `status` (`ACTIVE`/`DISABLED`), `preferred_currency` nullable, `time_zone` nullable, timestamps
- **Privacidad:** email y hash son PII/secretos; el hash no se exporta.
- **Notas:** verificación de email Q-014.

### group

- **Propósito:** espacio de gastos compartidos.
- **PK:** `id`
- **Atributos:** `name`, `currency_code` NOT NULL, `created_by` FK user, `status` (`ACTIVE`/`ARCHIVED`), timestamps

### group_membership

- **Propósito:** pertenencia y rol propuesto (`ADMIN`/`MEMBER`).
- **PK:** `id`
- **Única:** (`group_id`, `user_id`)
- **Atributos:** `role`, `status` (`INVITED`/`ACTIVE`/`LEFT`), `joined_at`, `left_at`
- **FK:** group, user

### group_invitation

- **Propósito:** token de invitación.
- **Atributos:** `group_id`, `email`, `token_hash`, `expires_at`, `accepted_at`, `created_by`
- **Privacidad:** guardar hash del token, no el token en claro. Caducidad Q-017.

### account

- **Propósito:** recipiente de saldo personal.
- **FK:** `user_id` NOT NULL (DEC-006)
- **Atributos:** `name`, `type` (`CASH`/`BANK`), `currency_code`, `initial_balance NUMERIC(19,2)`, `cached_balance NUMERIC(19,2)`, `status` (`ACTIVE`/`ARCHIVED`), `version` BIGINT (optimistic lock)
- **CHECK:** `currency_code` ~ ISO
- **Índice:** (`user_id`, `status`)

### category

- **Propósito:** clasificación.
- **Atributos:** `user_id` NULL = sistema, `name`, `kind` (`INCOME`/`EXPENSE`/`BOTH`), `archived`
- **Q-020** puede simplificar a solo sistema + user.

### transaction (ledger personal)

- **Propósito:** hecho económico personal.
- **FK:** `account_id`, `user_id` (desnormalizado para auth rápida, debe coincidir con la cuenta), `category_id` nullable, `transfer_id` nullable, `recurring_occurrence_id` nullable
- **Atributos:** `type` (`INCOME`/`EXPENSE`/`TRANSFER_IN`/`TRANSFER_OUT`), `amount NUMERIC(19,2)` CHECK > 0, `value_date` DATE, `description`, `status` (`POSTED`/`VOID`), `voided_at`, `voided_by`, `version`
- **Índices:** (`user_id`, `value_date`); (`account_id`, `value_date`); (`transfer_id`)
- **Integridad:** trigger o servicio: `user_id` = `account.user_id`; si transfer, exactamente un IN y un OUT, mismo amount y transfer_id, cuentas distintas, misma moneda.

### shared_expense

- **Propósito:** hecho canónico de gasto de grupo.
- **FK:** `group_id`, `payer_user_id`
- **Atributos:** `amount` > 0, `currency_code` (= group), `value_date`, `description`, `split_mode` (`EQUAL`/`EXACT`), `status` (`POSTED`/`VOID`), `version`
- **No FK a account** en MVP.

### shared_expense_split

- **Propósito:** participación.
- **FK:** `shared_expense_id`, `participant_user_id`
- **Única:** (`shared_expense_id`, `participant_user_id`)
- **Atributos:** `amount` > 0
- **Integridad:** suma de splits = `shared_expense.amount` (deferrable constraint o validación de aplicación + test). Participante con membership válida (Q-005 para exmiembros).

### settlement

- **Propósito:** liquidación entre dos miembros.
- **FK:** `group_id`, `from_user_id`, `to_user_id`
- **Atributos:** `amount` > 0, `currency_code`, `value_date`, `note`, `status` (`POSTED`/`VOID`)
- **CHECK:** `from_user_id <> to_user_id`

### budget

- **Should.** (`user_id`, `category_id`, `period_start`, `period_end`, `amount`, `currency_code`) UNIQUE lógica.

### savings_goal

- **Could.** `user_id`, `name`, `target_amount`, `target_date` nullable, `linked_account_id` nullable (Q no cerrada: ¿el progreso es saldo de cuenta o suma de movimientos etiquetados?). **No implementar** hasta decidir la métrica de progreso.

### debt

- **Should.** `user_id` (dueño del registro), `counterparty_name`, `direction` (`I_OWE`/`OWED_TO_ME`), `original_amount`, `currency_code`, `due_date` nullable, `status`

### debt_payment

- **Should.** `debt_id`, `amount` > 0, `value_date`, `transaction_id` nullable (Q-025), `status`

### recurring_rule

- **Should.** `user_id`, `account_id`, `category_id`, `type` ingreso/gasto, `amount`, `frequency` (`MONTHLY`/`WEEKLY` — conjunto mínimo), `next_date`, `status` (`ACTIVE`/`CANCELLED`)

### recurring_occurrence

- **Should.** `rule_id`, `due_date`, `status` (`PENDING`/`PAID`/`SKIPPED`), `transaction_id` nullable UNIQUE

### audit_event

- `actor_user_id`, `entity_type`, `entity_id`, `action`, `at`, `payload_json` (sin secretos)
- Append-only.

### idempotency_record

- Única (`user_id`, `key`); `request_hash`, `response_code`, `response_body`, `created_at`

## Relaciones y cardinalidades (resumen)

- user 1—N account
- user 1—N transaction
- account 1—N transaction
- user N—N group vía membership
- group 1—N shared_expense
- shared_expense 1—N split
- group 1—N settlement
- user 1—N budget, debt, recurring_rule

## Consistencia y concurrencia

- `account.version` y `shared_expense.version` para optimistic locking.
- Unicidad de splits evita duplicar un participante.
- Idempotencia evita doble POST.
- Transferencias: `transfer_id` NOT NULL en ambas piernas.

## Redondeo

Aplicar RN-035 **en aplicación** antes del INSERT; la BD solo verifica que la suma cuadra.

## Privacidad

Tablas con PII: `user`, `group_invitation.email`, `audit_event` (minimizar). Exportaciones leen vistas filtradas por autorización, no dumps.

## SavingsGoal y ExportJob

- `savings_goal`: candidata diferida (Could + pregunta de progreso).
- `ExportJob`: no se recomienda en MVP.
