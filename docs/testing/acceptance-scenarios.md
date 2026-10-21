# Escenarios de aceptación

**Estado:** documentados, **no ejecutados**. Datos en moneda ficticia `XXX`.

Formato: Given / When / Then. Relacionados con CU, US, RN.

## TS-001 — Registrar un ingreso

- **Given** Ada autenticada, cuenta Efectivo saldo 100.00
- **When** POST ingreso 50.00, fecha 2026-10-20, descripción “Nómina”
- **Then** 201; saldo 150.00; personal-summary del 2026-10: incomeTotal 50.00 (si no hay otros); type INCOME
- **CU-010, US-020, RF-020**

## TS-002 — Registrar un gasto

- **Given** saldo 150.00
- **When** gasto 30.00 categoría Comida
- **Then** saldo 120.00; expenseTotal incluye 30.00; no aparece en income ni como TRANSFER_*
- **CU-010, US-021, RF-021**

## TS-003 — Transferir entre cuentas

- **Given** Efectivo 120.00, Banco 20.00
- **When** transfer 40.00 Efectivo → Banco
- **Then** Efectivo 80.00, Banco 60.00; income y expense del período **iguales** que antes de la transferencia; existen dos legs con mismo transferId
- **And** repetir POST con la misma Idempotency-Key no crea cuatro legs
- **CU-011, US-022, RN-014**

## TS-004 — Crear un gasto compartido

- **Given** grupo Casa con Ada, Bob, Cory ACTIVE; Ada pagadora
- **When** shared-expense 90.00 EQUAL entre los tres
- **Then** tres splits de 30.00; gasto POSTED; **ninguna** transaction en el ledger de Ada por 90.00
- **CU-032, US-031, RN-023**

## TS-005 — Calcular participaciones (redondeo)

- **Given** mismo grupo
- **When** gasto 10.00 EQUAL entre 3, pagador Ada (participante)
- **Then** suma splits = 10.00; los tres importes difieren como mucho 0.01; el 0.01 extra va a Ada (RN-035)
- **And** EXACT 50+30+20 sobre 100 se acepta; 50+30+19 se rechaza 422 SPLIT_SUM_MISMATCH
- **US-032, US-033**

## TS-006 — Registrar una liquidación

- **Given** TS-004 (Bob debe 30 a Ada)
- **When** Bob POST settlement 30.00 to Ada
- **Then** par Bob→Ada ausente o 0.00; no hay segundo shared-expense; ledger de Bob sin gasto automático de 30.00
- **CU-033, US-034, RN-039**

## TS-007 — Consultar un informe

- **Given** TS-001, TS-002, TS-003 en octubre 2026 y TS-004 en el mismo mes
- **When** GET personal-summary octubre
- **Then** income 50.00, expense 30.00, result 20.00 (según la cadena de saldos de estos escenarios: ingreso 50 gasto 30; transfer ignorada; gasto compartido ignorado)
- **And** GET group balances sigue mostrando efectos de TS-004/TS-006 según se hayan aplicado
- **CU-020, RN-024**

Nota: si el orden de pruebas es aislado, usar fixtures fijos por escenario, no depender de un estado global sucio.

## TS-008 — Aplicar un presupuesto

- **Given** presupuesto Comida octubre 200.00; gastos comida 50 y 30 POSTED; una transferencia 25
- **When** GET budget
- **Then** spent 80.00 remaining 120.00; transferencia no suma
- **And** un shared-expense de restaurante **no** suma (RN-032) 
- **CU-040, Should**

## TS-009 — Registrar un pago recurrente

- **Given** regla alquiler 400.00 MONTHLY, ocurrencia due 2026-10-01 PENDING
- **When** llega 2026-10-01 **sin** confirmación
- **Then** sigue PENDING; cero transactions nuevas
- **When** confirm
- **Then** EXPENSE 400.00 vinculada; occurrence PAID
- **CU-050, RN-040, Should**

## TS-010 — Exportar respetando permisos

- **Given** movimientos de Ada y de Bob
- **When** Ada exporta CSV personal
- **Then** solo filas de Ada; Content-Type CSV
- **When** Ada GET export de Bob o de grupo donde no es miembro
- **Then** 404 o 401
- **When** Ada miembro exporta grupo (si RF-081 aprobado)
- **Then** CSV de actividad grupal sin cuentas personales de Bob
- **CU-060, RN-052**

## TS-011 — IDOR de cuenta (complementario)

- **Given** cuenta de Bob
- **When** Ada GET/PATCH/POST transaction con accountId de Bob
- **Then** 404/422; saldo de Bob intacto

## TS-012 — Anulación de gasto personal

- **Given** TS-002
- **When** void
- **Then** saldo restaurado; informe sin los 30.00; registro visible VOID

Estos escenarios son la referencia de QA. Hasta que existan, el estado es “especificado”.
