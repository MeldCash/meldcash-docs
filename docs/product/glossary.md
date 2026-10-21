# Glosario

**Estado:** propuesto, alineado con el brief. Los términos en inglés se mantienen cuando son de uso habitual en ingeniería.

| Término | Definición en MeldCash |
| --- | --- |
| Usuario | Persona autenticada que opera la plataforma. No implica todavía un rol de autorización definitivo. |
| Espacio personal | Conjunto de cuentas, movimientos, presupuestos y deudas que pertenecen a un único usuario y no se mezclan con el espacio grupal. |
| Grupo | Conjunto de usuarios que registran gastos compartidos y liquidaciones. |
| Miembro | Usuario con membresía activa (u otro estado que se apruebe) en un grupo. |
| Propietario / administrador de grupo | Perfil de análisis. Permisos concretos: ver Q-006 y RF-030. |
| Cuenta financiera | Recipiente de saldo (efectivo, banco u otro tipo aprobado) sobre el que se registran movimientos. |
| Movimiento / transacción | Registro de un hecho económico. Puede ser ingreso, gasto, transferencia o, si se aprueba, ajuste. |
| Ingreso | Incremento de saldo de una cuenta personal que no proviene de otra cuenta propia. |
| Gasto | Decremento de saldo de una cuenta personal que no es una transferencia a otra cuenta propia. |
| Transferencia | Movimiento interno entre dos cuentas del mismo usuario (o del alcance que se apruebe). No es ingreso ni gasto. |
| Categoría | Etiqueta de clasificación de movimientos para informes y presupuestos. |
| Saldo de cuenta | Resultado de saldo inicial más movimientos netos aplicables a esa cuenta. El significado de “saldo disponible” está abierto (Q-010). |
| Gasto compartido | Hecho económico de un grupo: alguien pagó un importe que se reparte entre participantes. No es, por sí mismo, un gasto personal duplicado en cada miembro. |
| Participación / split | Porción del gasto compartido asignada a un participante. |
| Balance de grupo | Resumen de quién debe a quién dentro de un grupo, derivado de gastos compartidos y liquidaciones, no de las cuentas personales. |
| Liquidación / settlement | Pago entre miembros que reduce o cancela un balance derivado de gastos compartidos. Distinto de registrar el gasto original. |
| Deuda personal | Obligación entre personas (o entidades) registrada en el espacio personal, distinta de un balance de grupo no liquidado. |
| Presupuesto | Límite de gasto planificado por categoría y período. |
| Meta de ahorro | Objetivo de acumulación con progreso medible. Prioridad de MVP: Could. |
| Regla recurrente | Definición de una obligación periódica (frecuencia, importe de referencia, próxima fecha). |
| Ocurrencia recurrente | Instancia de una regla en una fecha concreta. No equivale a un movimiento pagado. |
| Exportación | Descarga de un subconjunto de datos autorizado (CSV u otros formatos aprobados). |
| Idempotencia | Propiedad de una operación que, repetida con la misma clave, no duplica efectos financieros. |
| MVP | Producto mínimo validable: suficiente para demostrar flujos principales y recoger feedback, no una plataforma financiera completa. |
| Brief | Instrucciones de análisis emitidas por el propietario del proyecto (fuente de lo **confirmado** de producto). |

## Términos que no deben confundirse

1. **Gasto compartido** ≠ **gasto personal de cada participante**.
2. **Liquidación** ≠ **nuevo gasto**.
3. **Transferencia entre cuentas propias** ≠ **ingreso** y ≠ **gasto**.
4. **Deuda personal** ≠ **balance pendiente de un grupo**.
5. **Ocurrencia recurrente vencida** ≠ **movimiento pagado**.
6. **Saldo de cuenta** ≠ **balance entre miembros de un grupo**.
