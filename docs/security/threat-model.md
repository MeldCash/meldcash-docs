# Modelo de amenazas

**Estado:** cualitativo y **propuesto**. Probabilidad e impacto: Bajo / Medio / Alto, juicio de análisis, no estadística.

Activos: credenciales, movimientos, saldos, membresías, exportaciones, tokens de invitación, backups.

Atacantes: usuario malicioso autenticado, invitado abusivo, atacante de red, XSS en el navegador, operador de backups.

## Amenazas

| ID | Amenaza | Impacto | Prob. | Controles | Residual |
| --- | --- | --- | --- | --- | --- |
| THR-001 | Robo de sesión (XSS + cookie accesible) | Alto | Medio | HttpOnly, CSP, no tokens en localStorage | Medio si hay XSS en dependencias |
| THR-002 | Credential stuffing | Alto | Medio | Rate limit, hash fuerte, (Could) MFA | Medio |
| THR-003 | IDOR de movimientos/cuentas | Alto | Alto si no se prueba | Filtro por titular + tests | Bajo con disciplina |
| THR-004 | IDOR de grupo (enumerar UUID) | Alto | Medio | Membership check; 404 | Bajo |
| THR-005 | Duplicación financiera por reintento | Medio | Alto en móvil | Idempotency-Key | Bajo |
| THR-006 | Tampering de splits (suma ≠ total) | Medio | Medio | Validación RN-036 | Bajo |
| THR-007 | CSRF que crea transferencias | Alto | Medio | SameSite + CSRF | Bajo |
| THR-008 | Export masivo por miembro | Medio | Medio | Auth Q-013, rate limit | Medio (si todo el grupo es visible por diseño) |
| THR-009 | Fuga de token de invitación | Alto | Medio | Hash, caducidad, un uso, no loguear URL | Medio si se muestra al admin en pantalla |
| THR-010 | Invitación no solicitada (acoso) | Medio | Medio | Rate limit, aceptación, (Could) denylist | Medio |
| THR-011 | Usuario abandona y sigue leyendo | Medio | Medio | status membership Q-005 | Bajo si se implementa |
| THR-012 | SQL injection | Alto | Bajo con ORM parametrizado | Queries param | Bajo |
| THR-013 | Secretos en Git | Alto | Medio en equipos nuevos | .gitignore, revisión, no .env en repo | Medio operativo |
| THR-014 | Backup filtrado | Alto | Bajo-Medio | Cifrado de volumen, acceso restringido | Medio hasta Q-019 |
| THR-015 | Logs con PII excesiva | Medio | Medio | RNF-041, revisión | Medio |
| THR-016 | Fijación de sesión | Medio | Bajo | Regenerar SID | Bajo |
| THR-017 | Manipulación de `payerUserId` ajenos | Medio | Medio | Debe ser miembro; Q-006 quién puede registrar | Medio |
| THR-018 | Enumeración de emails en registro | Bajo | Alto | Mensajes genéricos vs UX | Aceptable o Medio |
| THR-019 | Clickjacking de login | Medio | Bajo | CSP frame-ancestors | Bajo |
| THR-020 | DoS de export/informe pesado | Medio | Medio | Límites de filas, paginación | Medio |

## Abusos de negocio (no solo técnicos)

- Registrar liquidaciones ficticias para “borrar” deudas de grupo.
- Anular gastos después de que otros ya pagaron (Q-028).
- Mitigación: auditoría visible, roles, no borrar historia.

## Fuera de modelo actual

Amenazas de open banking, fraude de tarjetas y malware en dispositivos de usuarios se reconocen pero no se controlan en el MVP más allá de HTTPS y buenas prácticas de sesión.
