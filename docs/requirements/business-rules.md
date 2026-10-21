# Reglas de negocio

**Estado:** **propuestas**, salvo mención al brief. Deben aprobarse antes de implementar cálculos.

## Dinero y precisión

| ID | Regla | Justificación | Depende de | Estado |
| --- | --- | --- | --- | --- |
| RN-001 | Los importes se representan con decimal exacto. La escala de almacenamiento propuesta es 2 dígitos (unidad menor de la moneda) para importes persistidos al usuario. | Evitar error de coma flotante. | Q-007 | Propuesto |
| RN-002 | Un movimiento tiene signo o tipo explícito; el importe persistido es siempre ≥ 0 y el tipo define el efecto. | Evitar ambigüedad. | — | Propuesto |
| RN-003 | Cero no es un movimiento válido. Importes negativos no se aceptan. | Validación. | — | Propuesto |
| RN-004 | En MVP se opera en **una moneda por cuenta**. Cruzar monedas en un mismo movimiento está prohibido hasta Q-007. | Simplicidad y no inventar FX. | Q-007 | Propuesto |
| RN-005 | Un grupo, en MVP, opera en **una moneda de grupo**. Todos los gastos y liquidaciones de ese grupo usan esa moneda. | Evitar conversión implícita. | Q-007 | Propuesto |

## Cuentas y saldos personales

| ID | Regla | Justificación | Estado |
| --- | --- | --- | --- |
| RN-008 | `saldo_cuenta = saldo_inicial + Σ efectos de movimientos no anulados`. El efecto de un ingreso es +importe; gasto −importe; transferencia según la pierna. | Consistencia. | Propuesto |
| RN-009 | El saldo puede ser negativo si no se aprueba lo contrario (Q-010). El MVP no bloquea gastos por saldo insuficiente salvo decisión. | No inventar “descubierto”. | Pendiente |
| RN-010 | Una cuenta pertenece a un único usuario en el MVP (DEC-006). | Q-001. | Propuesto |
| RN-011 | No se permite transferir hacia o desde una cuenta que no sea del mismo usuario. | Evitar mezclar espacios. | Propuesto |
| RN-012 | Anular un movimiento revierte su efecto y queda visible como anulado. Un movimiento anulado no se reedita; se registra uno nuevo si hace falta. | Historial. | Propuesto |
| RN-013 | Editar importe, cuenta, fecha o tipo de un movimiento no anulado recalcula saldos en la misma transacción. Si la edición viola reglas, se rechaza. | Q-009 puede restringir campos. | Propuesto |
| RN-014 | Transferencia: dos piernas con el mismo `transfer_id`, mismo importe, cuentas distintas, tipos `TRANSFER_OUT` y `TRANSFER_IN`. Ninguna pierna entra en totales de ingreso/gasto. | Brief A. | Propuesto |

## Informes personales

| ID | Regla | Justificación | Estado |
| --- | --- | --- | --- |
| RN-020 | Ingresos del período = suma de movimientos tipo ingreso no anulados cuya `fecha_valor` está en el período, en las cuentas del usuario, en la moneda del informe. | Brief B. | Propuesto |
| RN-021 | Gastos del período = análogo para tipo gasto. | Brief B. | Propuesto |
| RN-022 | Resultado = RN-020 − RN-021. Las transferencias no intervienen. | Brief A/B. | Propuesto |
| RN-023 | Un gasto compartido **no** incrementa automáticamente los gastos personales del pagador ni de los participantes. Si se desea reflejar “mi parte” en el espacio personal, será una operación explícita y aprobada (Q-002). | Evitar duplicación y confusión. | Propuesto |
| RN-024 | El dashboard personal no suma balances de grupo en el total de gastos. El resumen de grupo es otra vista (RF-045). | Brief: no confundir. | Propuesto |

## Presupuestos

| ID | Regla | Justificación | Estado |
| --- | --- | --- | --- |
| RN-030 | Consumo de presupuesto = gastos personales no anulados de esa categoría cuyo `fecha_valor` cae en el período del presupuesto. | Brief C. | Propuesto |
| RN-031 | Transferencias y liquidaciones no consumen presupuesto. | No son gasto. | Propuesto |
| RN-032 | Gastos compartidos no consumen presupuesto personal salvo Q-002. | Coherencia con RN-023. | Propuesto |
| RN-033 | Un presupuesto es único por (usuario, categoría, período, moneda). | Evitar dobles límites. | Propuesto |

## Gastos compartidos y redondeo

| ID | Regla | Justificación | Estado |
| --- | --- | --- | --- |
| RN-034 | Un gasto compartido tiene exactamente un pagador (usuario miembro) en el MVP. Pagadores múltiples: Could futuro. | Simplificar. | Propuesto |
| RN-035 | Partes iguales: se divide el importe entre N participantes con redondeo a la unidad menor. La diferencia de centavos se asigna al **pagador** si es participante; si no, al participante con mayor identificador estable ordenado (desempate determinista). | Cerrar la suma. | Propuesto |
| RN-036 | Importes personalizados: la suma de splits debe ser **exactamente** igual al importe del gasto; si no, HTTP 422 / error de validación. | No silenciar errores. | Propuesto |
| RN-037 | El pagador puede ser o no un participante que consume. Ejemplo: A paga la cena de B y C. Permitido. | Familias reales. | Propuesto |
| RN-038 | Balance(A, B) en un grupo = lo que B debería haber asumido en gastos pagados por A − lo que A debería haber asumido en gastos pagados por B − liquidaciones netas de A hacia B + liquidaciones netas de B hacia A. Fórmula equivalente por pares. | Un solo modelo. | Propuesto |
| RN-039 | Una liquidación registra que `from_user` pagó a `to_user` un importe > 0 en la moneda del grupo. No crea gasto compartido ni movimiento personal automático (Q-002, Q-004). | Evitar duplicar. | Propuesto |
| RN-041 | No se permite liquidación entre usuarios que no son (o fueron, según Q-005) miembros del grupo. | Autorización. | Propuesto |
| RN-042 | Anular un gasto compartido recálcula balances como si no existiera, y rechaza la anulación si hay liquidaciones que el propietario decida bloquear (Q-028). **Propuesta de MVP:** permitir anulación y recálculo; las liquidaciones permanecen como hechos de pago. | Conservar hechos de caja entre personas. | Pendiente |
| RN-043 | Un mismo hecho no se registra dos veces: la UI y la API no ofrecen “copiar este gasto a cada miembro como gasto personal” de forma implícita. | Brief F. | Propuesto |

## Recurrentes y deudas

| ID | Regla | Justificación | Estado |
| --- | --- | --- | --- |
| RN-040 | Llegar a la fecha de una ocurrencia **no** crea ni marca un movimiento como pagado. | Brief G. | Propuesto (alineado al brief) |
| RN-044 | Una ocurrencia pasa a `PAID` solo cuando el usuario confirma y se crea el movimiento vinculado. | Distinción obligación/pago. | Propuesto |
| RN-045 | El saldo pendiente de una deuda = importe original − suma de pagos no anulados. Nunca negativo: el último pago no puede exceder el pendiente. | Brief E. | Propuesto |
| RN-046 | Intereses no se calculan. | Brief: no aprobados. | Confirmado como exclusión |
| RN-047 | Una deuda personal no se genera automáticamente desde un balance de grupo. El usuario podría abrirla a mano si se aprueba (Q-023); el MVP no lo hace. | Evitar doble obligación. | Propuesto |

## Autorización y ciclo de vida

| ID | Regla | Justificación | Estado |
| --- | --- | --- | --- |
| RN-050 | Un usuario solo lee y escribe sus cuentas y movimientos personales. | IDOR. | Propuesto |
| RN-051 | Un usuario solo accede a datos de un grupo si su membresía está `ACTIVE` (u otro estado que se apruebe para históricos). | Q-005, Q-013. | Propuesto |
| RN-052 | La exportación aplica RN-050 y RN-051. | Brief H. | Propuesto |
| RN-053 | Eliminación de usuario: pendiente Q-016. No implementar borrado duro de historia financiera en el MVP sin política. | Conservación vs privacidad. | Pendiente |
| RN-054 | Fechas de valor se almacenan como `date` civil (sin hora) para movimientos, más `created_at` en timestamptz UTC. Informes agrupan por `date` civil. Zona horaria de visualización: Q-015. | Evitar saltos de día. | Propuesto |
