# Contratos de API

**Estado:** ilustrativos y **propuestos**. Ejemplos JSON, no implementación.

Los campos no aprobados se marcan. Importes como **string** decimal.

Convención de escritura Must: `Idempotency-Key: <uuid>`.

## Auth

### POST `/api/v1/auth/register`

Campos pendientes Q-014.

Solicitud:

```json
{
  "email": "ada@example.com",
  "password": "según-política-aprobada",
  "displayName": "Ada"
}
```

Respuesta 201:

```json
{
  "id": "11111111-1111-1111-1111-111111111111",
  "email": "ada@example.com",
  "displayName": "Ada"
}
```

La sesión se establece por cookie Set-Cookie (detalle de nombre no fijado). No devolver `password` ni hash.

### POST `/api/v1/auth/login`

```json
{ "email": "ada@example.com", "password": "…" }
```

200 + cookie. 401 genérico si falla.

### POST `/api/v1/auth/logout`

204. Invalida sesión.

### GET `/api/v1/me`

200: `{ "id", "email", "displayName" }`.

## Cuentas

### POST `/api/v1/accounts`

```json
{
  "name": "Efectivo",
  "type": "CASH",
  "currency": "XXX",
  "initialBalance": "100.00"
}
```

`currency` pendiente de catálogo real (Q-007). 201:

```json
{
  "id": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
  "name": "Efectivo",
  "type": "CASH",
  "currency": "XXX",
  "initialBalance": "100.00",
  "balance": "100.00",
  "status": "ACTIVE",
  "version": 0
}
```

### GET `/api/v1/accounts`

Lista solo del usuario de sesión.

## Movimientos

### POST `/api/v1/transactions`

```json
{
  "accountId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
  "type": "EXPENSE",
  "amount": "30.00",
  "valueDate": "2026-10-20",
  "categoryId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
  "description": "Comida"
}
```

Validación: `type` ∈ {INCOME, EXPENSE}; amount > 0; cuenta propia; categoría existente y usable.

201: recurso con `status: "POSTED"`, `version`, `balanceAfter` opcional (Could).

### GET `/api/v1/transactions`

Query: `from`, `to`, `accountId`, `categoryId`, `type`, `q`, `page`, `size`, `sort`.

No incluye gastos compartidos.

### POST `/api/v1/transactions/{id}/void`

If-Match: version. 200 recurso `VOID`.

Edición de descripción: `PATCH /api/v1/transactions/{id}` limitado (Q-009).

## Transferencias

### POST `/api/v1/transfers`

```json
{
  "fromAccountId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
  "toAccountId": "cccccccc-cccc-cccc-cccc-cccccccccccc",
  "amount": "40.00",
  "valueDate": "2026-10-20",
  "description": "Al banco"
}
```

201:

```json
{
  "transferId": "dddddddd-dddd-dddd-dddd-dddddddddddd",
  "amount": "40.00",
  "legs": [
    { "id": "…", "type": "TRANSFER_OUT", "accountId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa" },
    { "id": "…", "type": "TRANSFER_IN", "accountId": "cccccccc-cccc-cccc-cccc-cccccccccccc" }
  ]
}
```

422 `TRANSFER_SAME_ACCOUNT` o `CURRENCY_MISMATCH`.

## Informes

### GET `/api/v1/reports/personal-summary?from=2026-10-01&to=2026-10-31`

```json
{
  "from": "2026-10-01",
  "to": "2026-10-31",
  "currency": "XXX",
  "incomeTotal": "100.00",
  "expenseTotal": "40.00",
  "result": "60.00",
  "expensesByCategory": [
    { "categoryId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb", "name": "Comida", "amount": "40.00" }
  ],
  "accounts": [
    { "accountId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa", "balance": "60.00" }
  ]
}
```

Transferencias excluidas de income/expense. Multi-moneda: si el usuario tiene varias, Q-007 puede exigir `?currency=` o rechazar el agregado.

## Grupos

### POST `/api/v1/groups`

```json
{ "name": "Casa", "currency": "XXX" }
```

201: grupo + membership ADMIN.

### POST `/api/v1/groups/{groupId}/invitations`

Pendiente Q-017.

```json
{ "email": "bob@example.com" }
```

201: `{ "id", "email", "expiresAt" }` **sin token en claro** si el canal es email. Si el piloto no tiene SMTP, alternativa pendiente: devolver URL una sola vez al admin (riesgo THR-009).

### POST `/api/v1/groups/{groupId}/shared-expenses`

```json
{
  "payerUserId": "11111111-1111-1111-1111-111111111111",
  "amount": "90.00",
  "valueDate": "2026-10-20",
  "description": "Cena",
  "splitMode": "EQUAL",
  "participantUserIds": [
    "11111111-1111-1111-1111-111111111111",
    "22222222-2222-2222-2222-222222222222",
    "33333333-3333-3333-3333-333333333333"
  ]
}
```

Modo EXACT:

```json
{
  "payerUserId": "11111111-1111-1111-1111-111111111111",
  "amount": "100.00",
  "valueDate": "2026-10-20",
  "splitMode": "EXACT",
  "splits": [
    { "userId": "11111111-1111-1111-1111-111111111111", "amount": "50.00" },
    { "userId": "22222222-2222-2222-2222-222222222222", "amount": "30.00" },
    { "userId": "33333333-3333-3333-3333-333333333333", "amount": "20.00" }
  ]
}
```

201 incluye splits ya redondeados. 422 `SPLIT_SUM_MISMATCH`.

No incluye `accountId` (Q-002).

### GET `/api/v1/groups/{groupId}/balances`

```json
{
  "groupId": "eeeeeeee-eeee-eeee-eeee-eeeeeeeeeeee",
  "currency": "XXX",
  "pairs": [
    {
      "fromUserId": "22222222-2222-2222-2222-222222222222",
      "toUserId": "11111111-1111-1111-1111-111111111111",
      "amount": "30.00"
    }
  ]
}
```

Interpretación: `from` debe `amount` a `to`. Pares con 0 omitidos.

### POST `/api/v1/groups/{groupId}/settlements`

```json
{
  "fromUserId": "22222222-2222-2222-2222-222222222222",
  "toUserId": "11111111-1111-1111-1111-111111111111",
  "amount": "30.00",
  "valueDate": "2026-10-21",
  "note": "Bizum"
}
```

Validar que `fromUserId` es el llamante o que un admin puede registrar en nombre de otro (**pendiente Q-006**; recomendación MVP: el llamante debe ser `from` o `to`).

## Presupuestos (Should)

### POST `/api/v1/budgets`

```json
{
  "categoryId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
  "periodStart": "2026-10-01",
  "periodEnd": "2026-10-31",
  "amount": "200.00"
}
```

### GET `/api/v1/budgets/{id}`

Incluye `spent` y `remaining` calculados (RN-030).

## Recurrentes (Should)

### POST `/api/v1/recurring-rules`

Alta de regla. No crea transacciones.

### POST `/api/v1/recurring-occurrences/{id}/confirm`

Crea transacción. 201.

### POST `/api/v1/recurring-occurrences/{id}/skip`

200 `SKIPPED`.

## Deudas (Should)

### POST `/api/v1/debts` y POST `/api/v1/debts/{id}/payments`

Pago con `amount` ≤ pendiente. 422 `DEBT_OVERPAY`.

## Exportación

### GET `/api/v1/exports/personal-transactions?from=&to=&accountId=`

`Accept: text/csv` o `?format=csv`.

200 `text/csv; charset=utf-8`  
`Content-Disposition: attachment; filename="meldcash-transactions.csv"`

Columnas propuestas: `id,valueDate,type,amount,currency,accountName,categoryName,description,status`.

Misma autorización que GET transactions. No jobs.

### GET `/api/v1/exports/groups/{groupId}/activity` (Should)

CSV de shared expenses + settlements. Q-013.

## Lo que no se publica

- Endpoints de administración interna no pedidos.
- Dump de usuarios.
- Recálculo de saldo como API pública (puede ser operación interna).
