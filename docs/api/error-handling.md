# Manejo de errores de API

**Estado:** propuesto.

## Forma estable

```json
{
  "code": "VALIDATION_ERROR",
  "message": "La solicitud no es válida.",
  "correlationId": "6b2e0c7a-3d5a-4f1e-9c8b-1a2b3c4d5e6f",
  "details": [
    {
      "field": "amount",
      "code": "AMOUNT_NOT_POSITIVE",
      "message": "El importe debe ser mayor que cero."
    }
  ]
}
```

- `code`: constante estable para el cliente.
- `message`: legible; no incluye secretos ni SQL.
- `details`: opcional, errores de campo.
- `correlationId`: igual que el log.

No se envía stack trace, query, ni valores de password.

## Códigos HTTP

| HTTP | Uso |
| --- | --- |
| 200 | GET/PATCH OK |
| 201 | POST creación |
| 204 | Logout, algunos VOID si no se devuelve cuerpo (preferir 200 con recurso voided) |
| 400 | JSON mal formado |
| 401 | Sin sesión o sesión inválida |
| 403 | Sesión válida, operación no permitida (si se opta por no ocultar el recurso) |
| 404 | Recurso inexistente o ajeno (recomendado para IDOR) |
| 409 | Conflicto de versión o idempotency-key reutilizada con cuerpo distinto |
| 422 | Regla de negocio / validación de dominio |
| 429 | Rate limit (login, exportaciones) |
| 500 | Error no controlado |

## Códigos de aplicación (propuestos)

| code | HTTP | Significado |
| --- | --- | --- |
| VALIDATION_ERROR | 422 | Campos |
| UNAUTHENTICATED | 401 | |
| FORBIDDEN | 403 | |
| NOT_FOUND | 404 | |
| IDEMPOTENCY_CONFLICT | 409 | Misma key, distinto hash |
| VERSION_CONFLICT | 409 | If-Match |
| TRANSFER_SAME_ACCOUNT | 422 | |
| CURRENCY_MISMATCH | 422 | |
| SPLIT_SUM_MISMATCH | 422 | RN-036 |
| ALREADY_VOIDED | 422 | |
| MEMBERSHIP_REQUIRED | 403/404 | |
| OPEN_BALANCE_BLOCK | 422 | Abandono de grupo Q-005 |
| DEBT_OVERPAY | 422 | RN-045 |
| RATE_LIMITED | 429 | |

## Validación

- Tipos y rangos en Bean Validation → `VALIDATION_ERROR`.
- Invariantes de agregado en dominio → código específico.
- Fallos de FK inesperados → 500 (no deberían llegar).

## Idempotencia

- Primera vez: se ejecuta y se guarda respuesta.
- Misma key y mismo hash: se devuelve la respuesta guardada con el mismo HTTP.
- Misma key y distinto hash: 409 `IDEMPOTENCY_CONFLICT`.

## Errores de exportación

Mismos códigos de autorización que GET del recurso. Un CSV no es un canal para eludir 404/401.
