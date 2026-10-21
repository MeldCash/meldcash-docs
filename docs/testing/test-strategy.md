# Estrategia de pruebas

**Estado:** plan **propuesto** para cuando exista implementación. **Ninguna prueba se ha ejecutado.** No hay suite en el repositorio.

## Objetivos

Detectar antes de un piloto: duplicación de dinero, fugas de autorización, errores de redondeo y regresiones de flujos Must.

## Pirámide

| Capa | Qué | Herramientas propuestas | Cuándo |
| --- | --- | --- | --- |
| Unitaria | Redondeo RN-035, RN-038, validaciones, mapeos | JUnit, Vitest | Cada PR |
| Integración | TX atómicas, constraints, cache de saldo | Testcontainers PostgreSQL | Cada PR |
| API | Contratos HTTP, errores, idempotencia | MockMvc / REST Assured | Cada PR |
| Autorización | IDOR sistemático | API tests con dos usuarios | Cada PR |
| E2E | Flujos Must en UI adaptable | Playwright desktop + un viewport móvil | Main / nightly |
| Exploratoria | Claridad de espacios personal/grupo | QA manual en piloto | Antes de feedback de usuarios |

## Tipos exigidos por el brief

### Unitarias

- RN-035: 10.00 / 3; 90.00 / 3; 0.01 / 2 (rechazo o regla).
- RN-038 ejemplos de A/B/C.
- RN-014 clasificación transferencia ≠ ingreso.

### Integración

- Transferencia commit/rollback.
- Gasto compartido: cabecera sin splits no persiste.
- `cached_balance` = recomputo RN-008.

### API

- Códigos HTTP del contrato.
- Idempotency replay y conflicto.

### Autorización

Matriz: usuario A, usuario B, grupo G (A y B), grupo H (solo A).

Cada GET/POST Must se prueba cruzado. Esperado: 401 anónimo, 404/403 cruzado.

### Consistencia financiera

- Ingreso y gasto actualizan saldo.
- Anulación revierte.
- Concurrencia: dos gastos paralelos sobre la misma cuenta; resultado = ambos o uno 409 y reintento consistente (no saldo perdido).

### Gastos compartidos y liquidaciones

- TS-004 a TS-006.
- Anulación de gasto (Q-028).

### Redondeo

- Suma de splits = total, siempre, en 20 casos parametrizados.

### Concurrentes

- Dos liquidaciones simultáneas; dos POSTs mismo Idempotency-Key.

### Exportación

- CSV columnas; no aparecen filas de otro usuario; BOM/UTF-8; comas en descripciones escapadas.

### Accesibilidad y adaptable

- Playwright a 375px y 1280px en login, dashboard, alta de gasto, grupo.
- Checks automáticos axe-core Could; manual Should para foco y labels.

### Regresión

- Lista de flujos Must como smoke E2E en CI.

## Datos de prueba ilustrativos

Nunca datos reales de personas.

| Fixture | Uso |
| --- | --- |
| ada@example.test / bob@example.test / cory@example.test | Tres miembros |
| Cuentas Ada Cash 100.00, Bank 20.00 moneda `XXX` | Ledger |
| Grupo Casa, moneda `XXX` | Splits |

`example.test` es reservado; no enviar emails reales.

## Criterios de calidad de la suite

- Un bug de IDOR es bloqueante de release de piloto.
- Un bug de doble conteo de transferencia es bloqueante.
- Cobertura de líneas no es el KPI; sí lo es cubrir RN-014, RN-023, RN-035, RN-038, RN-040, RN-050.

## Lo que este plan no hace

No sustituye pentest. No prueba cumplimiento legal. No prueba proveedores SMTP hasta que existan.
