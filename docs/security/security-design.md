# Diseño de seguridad

**Estado:** controles **propuestos** para un MVP web financiero. No hay implementación que auditar. No se afirma cumplimiento de normas.

## Objetivos

1. Confidencialidad de movimientos, cuentas y grupos.
2. Integridad de importes y hechos (no alteración silenciosa).
3. Disponibilidad razonable del piloto (no DDoS enterprise).
4. No mezclar autorizaciones entre usuarios (IDOR).

## Autenticación

- Passwords: Argon2id o BCrypt con factor de coste actualizado; nunca reversible.
- Política de contraseña: **pendiente de aprobación** (no se inventa longitud mínima como decisión de negocio; se recomienda ≥ 12 y denylist de passwords comunes).
- Sesión: identificador aleatorio criptográfico, cookie `HttpOnly`, `Secure` en no-local, `SameSite=Lax` o `Strict`.
- Rotación / idle timeout: valores a aprobar; no se fijan minutos aquí.
- Reset: token de un uso, hash en BD, caducidad corta (Q-011).
- Rate limit en login y reset.
- MFA: Could.

## Autorización

- Cada query filtra por `user_id` o membership. Tests de IDOR obligatorios (estrategia de pruebas).
- No confiar en IDs enviados por el cliente para titularidad: el servidor cruza con sesión.
- Roles de grupo mínimos: ADMIN y MEMBER (propuestos).
- Horizontal: usuario A no lee cuenta, transacción, presupuesto, deuda ni exportación de B.
- Vertical: MEMBER no invita si solo ADMIN puede (Q-006).

## Validación

- Listas blancas de enums.
- Límites de tamaño de string.
- Importes con regex decimal o parser `BigDecimal` estricto, escala 2.
- Rechazo de documentos inesperados (no hay upload en MVP: reduce riesgo).

## Ataques web habituales (baseline OWASP)

| Riesgo | Control propuesto |
| --- | --- |
| XSS | React escape por defecto; CSP; no `dangerouslySetInnerHTML` |
| CSRF | Token o SameSite + cookie no `SameSite=None` sin CSRF |
| SQLi | Bind parameters / query builder |
| Clickjacking | `X-Frame-Options` / CSP `frame-ancestors` |
| Session fixation | Regenerar ID al login |
| Mass assignment | DTO explícitos |
| Open redirect | No redirigir a URLs arbitrarias en invitaciones |

## Secretos

- Variables de entorno o secret manager. **No** commitear secretos.
- Este repositorio no debe contener `.env` reales.
- Rotación: procedimiento pendiente de ops.

## Tránsito y almacenamiento

- TLS 1.2+ en piloto y posteriores.
- Local: HTTP aceptable solo en loopback.
- BD: cifrado en reposo **si el proveedor lo ofrece**; no afirmar cifrado a nivel de columna en MVP salvo decisión.
- Backups: mismo control de acceso que producción; Q-019.

## Logs de seguridad

Registrar: login OK/KO (sin password), logout, reset solicitado, invitaciones, voids, exportaciones, 401/403 masivos.

No registrar: password, cookie, token, IBAN completo, CSV completo.

## Exportaciones

- Auth igual que GET.
- No cachear CSV en CDN público.
- Filename sin PII excesiva.
- Rate limit.

## Invitaciones a grupos (Q-017)

- Token suficiente entropía; almacenar hash.
- Caducidad.
- Un uso.
- No filtrar si un email está registrado (mensajes genéricos) cuando sea posible sin romper UX — tensionar con usabilidad; documentar decisión.
- El admin no debería poder añadir a un usuario a un grupo **sin** aceptación si esa es la política (recomendación: requiere aceptación).

## Retención y eliminación

Q-016, Q-018, Q-019. Hasta entonces: no borrar hechos; deshabilitar usuario (`DISABLED`) como mínimo operativo.

## Cabeceras de seguridad propuestas

`Content-Security-Policy`, `Referrer-Policy`, `X-Content-Type-Options: nosniff`, HSTS en HTTPS.

## Lo que no está en alcance de seguridad del MVP

- HSM, WAF de pago, bug bounty, pentest formal (recomendable antes de datos reales masivos, no afirmado como hecho).
- PCI DSS: **no** se deben almacenar PAN de tarjetas; el tipo de cuenta BANK es un apodo, no un número de tarjeta. Si en el futuro se piden números de cuenta, hay que reabrir el modelo de amenazas.
