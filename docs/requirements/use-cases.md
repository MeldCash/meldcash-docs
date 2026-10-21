# Casos de uso

**Estado:** **propuestos**. Nivel de análisis, no especificación de pantallas finales.

Actores genéricos: Visitante, Usuario autenticado, Miembro de grupo, Administrador de grupo (propuesto).

## CU-001 — Crear cuenta de usuario e iniciar sesión

- Actor: Visitante
- Precondición: no hay sesión
- Flujo básico: completar alta → validar → persistir usuario → iniciar sesión
- Alternativos: correo duplicado; validación fallida
- Postcondición: existe usuario; sesión válida
- RF: RF-001, RF-002
- Preguntas: Q-011, Q-014

## CU-010 — Registrar movimiento personal

- Actor: Usuario
- Precondición: al menos una cuenta propia
- Flujo: elegir tipo (ingreso/gasto) → cuenta → importe → fecha → categoría → guardar
- Validaciones: RN-002, RN-003, RN-050
- Postcondición: movimiento persistido; saldo actualizado en la misma transacción
- RF: RF-020, RF-021

## CU-011 — Transferir entre cuentas propias

- Actor: Usuario
- Flujo: cuenta origen, destino, importe, fecha → confirmar
- Sistema crea dos piernas enlazadas (RN-014) con idempotencia (RNF-003)
- Excepciones: misma cuenta; cuenta ajena; moneda distinta (RN-004)
- RF: RF-022

## CU-012 — Anular movimiento

- Actor: Usuario
- Flujo: seleccionar movimiento no anulado → confirmar anulación
- Postcondición: estado `VOID`; saldo revertido
- RF: RF-024

## CU-020 — Consultar dashboard personal

- Actor: Usuario
- Flujo: elegir período → sistema calcula RN-020–RN-022 y RF-041–RF-042
- No incluye balances de grupo
- RF: RF-040

## CU-030 — Crear grupo

- Actor: Usuario
- Flujo: nombre, moneda de grupo → persistir grupo y membresía admin
- RF: RF-030

## CU-031 — Invitar y aceptar miembro

- Actor: Administrador / invitado
- Flujo depende de Q-017 (enlace mágico, código, email)
- Postcondición: membresía `ACTIVE`
- Excepción: invitación caducada o reutilizada según política pendiente
- RF: RF-031

## CU-032 — Registrar gasto compartido

- Actor: Miembro
- Precondición: membresía activa
- Flujo: importe, pagador, participantes, modo de split, fecha, nota
- Sistema valida suma y redondeo (RN-035/036) y persiste gasto + splits atómicamente
- Postcondición: balances consultables vía RN-038
- No crea movimientos personales (RN-023)
- RF: RF-034–RF-036

## CU-033 — Registrar liquidación

- Actor: Miembro
- Flujo: from, to, importe, fecha, nota
- Validar miembros y moneda
- Postcondición: el hecho de pago queda registrado; balances netos cambian
- RF: RF-038

## CU-034 — Consultar balances de grupo

- Actor: Miembro
- Flujo: abrir grupo → listar pares o lista “tú debes / te deben”
- Datos: solo derivados de gastos no anulados y liquidaciones no anuladas
- RF: RF-037, RF-045

## CU-040 — Aplicar presupuesto

- Actor: Usuario
- Flujo: categoría, período, límite → guardar → al consultar, RN-030
- RF: RF-050, RF-051
- Prioridad: Should

## CU-050 — Confirmar ocurrencia recurrente

- Actor: Usuario
- Precondición: existe ocurrencia `PENDING`
- Flujo: confirmar → crear movimiento → vincular → estado `PAID`
- Alternativo: omitir (RF-073)
- RN-040 impide auto-pago
- RF: RF-072

## CU-060 — Exportar movimientos

- Actor: Usuario o miembro
- Flujo: elegir espacio (personal o grupo) → período → generar CSV
- Autorización RN-052
- RF: RF-080, RF-081

## CU-070 — Registrar pago de deuda personal

- Actor: Usuario
- Flujo: seleccionar deuda → importe ≤ pendiente → persistir pago → opcionalmente movimiento (Q-025)
- RF: RF-061
- Prioridad: Should

## Relación caso de uso ↔ escenario de prueba

| Caso de uso | Escenarios |
| --- | --- |
| CU-010 | TS-001, TS-002 |
| CU-011 | TS-003 |
| CU-032 | TS-004, TS-005 |
| CU-033 | TS-006 |
| CU-020 | TS-007 |
| CU-040 | TS-008 |
| CU-050 | TS-009 |
| CU-060 | TS-010 |
