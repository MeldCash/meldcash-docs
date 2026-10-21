# Requisitos funcionales

**Estado de cada ítem:** indicado por fila. Ningún requisito derivado solo de interpretación está marcado como confirmado.  
**Prioridad:** propuesta MoSCoW para el MVP.  
**Fuente típica:** brief del propietario, salvo que se indique “interpretación de análisis”.

Prioridades: M = Must, S = Should, C = Could, W = Won't MVP.

## Identidad y sesión

| ID | Nombre | Descripción | Justificación | Actor | Pri | Estado | Fuente |
| --- | --- | --- | --- | --- | --- | --- | --- |
| RF-001 | Registro de usuario | Una persona puede crear una cuenta con los datos de identidad que se aprueben. | Acceso al espacio personal. | Visitante | M | Pendiente (campos: Q-014) | Brief (implícito para plataforma multi-usuario) + análisis |
| RF-002 | Autenticación | Un usuario registrado inicia sesión de forma segura. | Proteger datos financieros. | Usuario | M | Pendiente (mecanismo: Q-011) | Análisis |
| RF-003 | Cierre de sesión | El usuario termina la sesión activa. | Control de acceso. | Usuario | M | Propuesto | Análisis |
| RF-004 | Recuperación de cuenta | El usuario puede recuperar el acceso si lo pierde. | Operación continua. | Usuario | S | Pendiente (Q-011) | Análisis |
| RF-005 | Gestión de perfil básico | Consultar y actualizar datos de perfil no sensibles según política. | Corrección de identidad. | Usuario | S | Pendiente | Análisis |

## Cuentas financieras

| ID | Nombre | Descripción | Justificación | Actor | Pri | Estado | Fuente |
| --- | --- | --- | --- | --- | --- | --- | --- |
| RF-010 | Alta de cuenta | Crear cuenta (efectivo, banco u otro tipo aprobado) con nombre, tipo, moneda y saldo inicial. | Administrar cuentas. | Usuario | M | Propuesto | Brief D |
| RF-011 | Listar y consultar cuentas | Ver cuentas propias y su saldo calculado. | Comprensión de posición. | Usuario | M | Propuesto | Brief D |
| RF-012 | Editar metadatos de cuenta | Cambiar nombre o estado (activa/archivada) sin reescribir historia. | Mantenimiento. | Usuario | S | Propuesto | Análisis |
| RF-013 | Archivar cuenta | Ocultar una cuenta sin borrar movimientos. | Evitar pérdida de historia. | Usuario | S | Propuesto | Análisis |

## Movimientos personales

| ID | Nombre | Descripción | Justificación | Actor | Pri | Estado | Fuente |
| --- | --- | --- | --- | --- | --- | --- | --- |
| RF-020 | Registrar ingreso | Crear ingreso en una cuenta propia con fecha, importe, categoría opcional/obligatoria (Q-024) y descripción. | Registro de movimientos. | Usuario | M | Propuesto | Brief A |
| RF-021 | Registrar gasto | Análogo a RF-020 para gasto. | Registro de movimientos. | Usuario | M | Propuesto | Brief A |
| RF-022 | Transferencia entre cuentas | Registrar salida y entrada enlazadas entre dos cuentas del mismo usuario, sin clasificarlas como ingreso/gasto. | Evitar duplicación y distorsión. | Usuario | M | Propuesto | Brief A |
| RF-023 | Editar movimiento | Modificar campos permitidos con validación y rastro. | Corrección. | Usuario | M | Pendiente (Q-009) | Brief A |
| RF-024 | Anular movimiento | Marcar un movimiento como anulado, revirtiendo efecto en saldos, sin desaparecer del historial. | Eliminación controlada. | Usuario | M | Propuesto | Brief A |
| RF-025 | Filtrar y buscar | Filtrar por período, cuenta, tipo, categoría y texto. | Comprensión. | Usuario | M | Propuesto | Brief A |
| RF-026 | Historial de cambios | Consultar quién/qué cambió un movimiento. | Trazabilidad. | Usuario | S | Propuesto | Brief A |
| RF-027 | Categorías | Listar categorías del sistema y, si se aprueba, propias. | Informes y presupuestos. | Usuario | M | Pendiente (Q-024) | Brief A |

## Informes y dashboard

| ID | Nombre | Descripción | Justificación | Actor | Pri | Estado | Fuente |
| --- | --- | --- | --- | --- | --- | --- | --- |
| RF-040 | Resumen del período | Mostrar ingresos, gastos y resultado del período en espacio personal, excluyendo transferencias. | Comprender movimientos. | Usuario | M | Propuesto | Brief B |
| RF-041 | Saldos de cuentas | Mostrar saldo de cada cuenta propia. | Objetivo de cuentas. | Usuario | M | Propuesto | Brief B, D |
| RF-042 | Distribución por categoría | Gastos del período agrupados por categoría. | Comprensión. | Usuario | M | Propuesto | Brief B |
| RF-043 | Evolución de saldo | Serie temporal de saldo personal agregado o por cuenta. | Brief B | Usuario | S | Propuesto | Brief B |
| RF-044 | Comparación de períodos | Comparar dos períodos. | Brief B | Usuario | S | Propuesto | Brief B |
| RF-045 | Resumen de grupo | Mostrar balances entre miembros y totales de gastos compartidos del grupo, no mezclados con el dashboard personal por defecto. | Finanzas compartidas. | Miembro | M | Propuesto | Brief B, F |

## Presupuestos y metas

| ID | Nombre | Descripción | Justificación | Actor | Pri | Estado | Fuente |
| --- | --- | --- | --- | --- | --- | --- | --- |
| RF-050 | Crear presupuesto | Definir límite por categoría y período. | Organizar presupuestos. | Usuario | S | Propuesto | Brief C |
| RF-051 | Consumo vs presupuesto | Calcular gasto real aplicable contra el límite. | Desviación. | Usuario | S | Propuesto | Brief C |
| RF-052 | Indicador de desviación | Mostrar estado bajo/en/sobre presupuesto en la UI. | Brief condiciona alertas. | Usuario | S | Propuesto | Brief C |
| RF-053 | Alertas de desviación | Notificar al superar umbral. | Condicionado a aprobación. | Usuario | C | Pendiente | Brief C |
| RF-054 | Metas de ahorro | Crear meta, importe objetivo y progreso. | Brief C | Usuario | C | Propuesto | Brief C |

## Deudas personales

| ID | Nombre | Descripción | Justificación | Actor | Pri | Estado | Fuente |
| --- | --- | --- | --- | --- | --- | --- | --- |
| RF-060 | Registrar deuda | Acreedor, deudor, importe original, pendiente, vencimiento, notas. | Controlar deudas. | Usuario | S | Propuesto | Brief E |
| RF-061 | Registrar pago de deuda | Pago parcial o total que reduce el pendiente y, si se aprueba, genera movimiento de cuenta. | Consistencia. | Usuario | S | Pendiente (Q-025) | Brief E |
| RF-062 | Listar deudas abiertas | Ver pendientes. | Control. | Usuario | S | Propuesto | Brief E |

## Grupos y gastos compartidos

| ID | Nombre | Descripción | Justificación | Actor | Pri | Estado | Fuente |
| --- | --- | --- | --- | --- | --- | --- | --- |
| RF-030 | Crear grupo | Un usuario crea un grupo y queda como administrador inicial (propuesto). | Gastos compartidos. | Usuario | M | Propuesto | Brief F |
| RF-031 | Invitar miembros | Incorporar a otros usuarios según el flujo aprobado. | Colaboración. | Admin de grupo (propuesto) | M | Pendiente (Q-017) | Brief F |
| RF-032 | Listar miembros y estado | Ver membresías. | Administración. | Miembro | M | Propuesto | Brief F |
| RF-033 | Abandonar grupo | Un miembro deja de participar según reglas de saldos abiertos. | Ciclo de vida. | Miembro | S | Pendiente (Q-005) | Análisis |
| RF-034 | Registrar gasto compartido | Quién pagó, participantes, importe, fecha, nota, modo de reparto. | Núcleo grupal. | Miembro | M | Propuesto | Brief F |
| RF-035 | Reparto igualitario | Dividir en partes iguales con regla de redondeo documentada. | Mínimo MVP. | Sistema | M | Propuesto | Brief F |
| RF-036 | Reparto por importes | El usuario asigna importes que suman el total. | Familias reales. | Miembro | M | Propuesto | Brief F |
| RF-037 | Consultar balances | Ver quién debe a quién. | Comprensión grupal. | Miembro | M | Propuesto | Brief F |
| RF-038 | Registrar liquidación | Pago entre dos miembros que reduce el balance, sin crear un gasto compartido nuevo. | Evitar duplicación. | Miembro | M | Propuesto | Brief F |
| RF-039 | Editar/anular gasto compartido | Cambio controlado con recálculo de balances. | Historial. | Pendiente (Q-006) | S | Pendiente | Brief F |
| RF-046 | Historial de cambios grupales | Auditoría de gasto y liquidación. | Brief F | Miembro autorizado | S | Propuesto | Brief F |

## Recurrentes

| ID | Nombre | Descripción | Justificación | Actor | Pri | Estado | Fuente |
| --- | --- | --- | --- | --- | --- | --- | --- |
| RF-070 | Crear regla recurrente | Frecuencia, importe de referencia, cuenta, categoría, próxima fecha. | Obligaciones periódicas. | Usuario | S | Propuesto | Brief G |
| RF-071 | Consultar ocurrencias | Lista con estado. | Control. | Usuario | S | Propuesto | Brief G |
| RF-072 | Confirmar pago de ocurrencia | Crea el movimiento real de forma explícita. | Distinguir obligación de pagado. | Usuario | S | Propuesto | Brief G |
| RF-073 | Omitir ocurrencia | Marcar como no pagada / no aplica, sin crear movimiento. | Cancelaciones puntuales. | Usuario | S | Propuesto | Brief G |
| RF-074 | Modificar o cancelar regla | Cambia futuras ocurrencias; impacto en pasadas: Q-026. | Brief G | Usuario | S | Pendiente | Brief G |
| RF-075 | Recordatorios | Aviso antes o en la fecha. | Condicionado. | Usuario | C | Pendiente | Brief G |

## Exportación

| ID | Nombre | Descripción | Justificación | Actor | Pri | Estado | Fuente |
| --- | --- | --- | --- | --- | --- | --- | --- |
| RF-080 | Exportar CSV personal | Descargar movimientos del espacio personal filtrados, si el usuario está autorizado sobre esos datos. | Brief H | Usuario | M | Propuesto | Brief H |
| RF-081 | Exportar CSV de grupo | Descargar gastos y liquidaciones del grupo si el permiso lo permite. | Brief H | Miembro | S | Pendiente (Q-013) | Brief H |
| RF-082 | Exportar Excel | Igual contenido en hoja de cálculo. | Brief H | Usuario | C | Propuesto | Brief H |
| RF-083 | Exportar PDF | Informe resumido. | Brief H | Usuario | C | Propuesto | Brief H |

## Criterios de aceptación transversales (propuestos)

Cada RF Must debe poder verificarse con:

1. Un actor autorizado completa la operación y persiste el resultado.
2. Un actor no autorizado recibe rechazo sin filtrado de datos ajenos.
3. La operación no duplica hechos económicos (especialmente RF-022, RF-034, RF-038).
4. Los importes respetan la precisión y el redondeo definidos (RN-001, RN-035).

Criterios detallados por flujo: [historias](user-stories.md) y [escenarios](../testing/acceptance-scenarios.md).
