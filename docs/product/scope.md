# Alcance

**Estado:** clasificación **propuesta** (MoSCoW) hasta que el propietario apruebe el alcance.  
**Fuente de las áreas a analizar:** brief, secciones A–H.  
**Priorización:** propuesta del equipo de análisis, no un compromiso de entrega.

Leyenda MoSCoW aplicada al **MVP validable**:

- **Must:** imprescindible para demostrar el concepto sin duplicar dinero ni mezclar espacios.
- **Should:** importante para un piloto útil; aplazable si bloquea la validación.
- **Could:** valioso, no necesario para el primer piloto.
- **Won't (MVP):** explícitamente fuera del primer corte; puede entrar después.

## A. Registro de movimientos financieros

| Capacidad | MoSCoW | Comentario |
| --- | --- | --- |
| Registrar ingreso y gasto personal | Must | Núcleo del espacio personal. |
| Categoría, fecha, importe, descripción | Must | Moneda: ver Q-007. |
| Edición controlada | Must | Reglas de qué se puede cambiar: RN-012, Q-009. |
| Eliminación controlada (anulación) | Must | Preferible anular a borrar físico (propuesto). |
| Filtros y búsqueda básicos | Must | Por fecha, cuenta, categoría, tipo, texto. |
| Historial de operaciones | Should | Auditoría de cambios; mínimo Must sobre anulación. |
| Transferencia entre cuentas propias | Must | No debe figurar como ingreso/gasto. |
| Adjuntos (fotos de tickets) | Won't | Fuera del MVP. |
| Importación bancaria | Won't | Fuera del MVP. |

## B. Informes y dashboard

| Indicador / vista | MoSCoW | Estado de definición |
| --- | --- | --- |
| Total ingresos del período (espacio personal) | Must | Confirmado como área a cubrir; fórmula propuesta RN-020. |
| Total gastos del período (espacio personal) | Must | Igual. |
| Resultado del período (ingresos − gastos) | Must | Transferencias excluidas. |
| Saldo por cuenta | Must | Definición de “disponible” pendiente (Q-010). |
| Evolución del saldo | Should | Serie temporal básica. |
| Distribución por categorías | Must | Gastos del período. |
| Comparación entre dos períodos | Should | Puede ser un segundo corte. |
| Resumen de finanzas compartidas (balances de grupo) | Must | Distinto del dashboard personal. |
| Vista combinada personal + grupal | Won't / Could | Riesgo de confusión; no recomendada en MVP (DEC-007). |
| Pronósticos | Won't | Fuera del MVP. |

## C. Presupuestos y metas de ahorro

| Capacidad | MoSCoW | Notas |
| --- | --- | --- |
| Presupuesto por categoría y período | Should | Útil para validar “organizar presupuestos”, aplazable si el piloto se centra en movimientos + grupos. |
| Comparación real vs presupuesto | Should | Depende de RN-030. |
| Alertas de desviación | Could | El brief las condiciona a aprobación. |
| Metas de ahorro y progreso | Could | No bloquea el MVP de movimientos y grupos. |

**Tensión de alcance:** el objetivo general menciona presupuestos, pero un MVP validable puede demostrar el concepto sin metas de ahorro. Se recomienda Must solo si el propietario considera que sin presupuestos el piloto no es creíble. Hasta esa decisión: **Should**.

## D. Cuentas financieras

| Capacidad | MoSCoW | Notas |
| --- | --- | --- |
| Efectivo y cuenta bancaria | Must | Tipos mínimos. |
| Otras cuentas | Could | Tras aprobar tipos (Q-022). |
| Saldo inicial | Must | RN-008. |
| Movimientos asociados | Must | |
| Transferencias | Must | |
| Conciliación / ajustes | Could | El brief no los da por aprobados. |
| Cuentas compartidas entre personas | Pendiente | Q-001. Recomendación MVP: no (DEC-006). |

## E. Deudas y préstamos

| Capacidad | MoSCoW | Notas |
| --- | --- | --- |
| Registrar deuda personal (acreedor/deudor, original, pendiente, vencimiento) | Should | Diferenciar de balances de grupo. |
| Pagos parciales | Should | |
| Relación con personas o grupos | Pendiente | Q-023. |
| Intereses, penalizaciones, calendarios automáticos | Won't | Brief: no darlos por aprobados. |

Si el tiempo de piloto es corto, las deudas personales pueden aplazarse porque el valor diferencial está en **gastos compartidos**. Siguen en el objetivo general, por eso **Should**, no Won't.

## F. Gastos compartidos

| Capacidad | MoSCoW | Notas |
| --- | --- | --- |
| Crear y administrar grupos | Must | |
| Invitación de miembros | Must | Mecanismo exacto: Q-017. |
| Registrar gasto compartido (quién pagó, participantes, importe) | Must | |
| Reparto partes iguales | Must | Mínimo recomendado. |
| Reparto por importes personalizados | Must | Cubre casos reales de familia. |
| Reparto por porcentajes | Could | Derivado de importes. |
| Cálculo de pendientes entre participantes | Must | |
| Liquidaciones | Must | |
| Historial de cambios | Should | |
| Simplificación de deudas (netting multinodo avanzado) | Could | El MVP puede mostrar balances por par de usuarios. |

## G. Pagos recurrentes y recordatorios

| Capacidad | MoSCoW | Notas |
| --- | --- | --- |
| Alta de obligación periódica | Should | |
| Frecuencia y próxima fecha | Should | |
| Estado de ocurrencia (pendiente / registrada como pagada / omitida) | Should | Nunca auto-pagada solo por fecha (RN-040). |
| Recordatorios (email/push) | Could | Condicionados a aprobación. |
| Generación automática de movimiento al confirmar | Should | Acción explícita del usuario. |

## H. Exportaciones

| Capacidad | MoSCoW | Notas |
| --- | --- | --- |
| CSV de movimientos personales | Must | Formato mínimo para validar. |
| CSV de un grupo (según permisos) | Should | Q-013. |
| Excel | Could | |
| PDF de informe | Could | |
| Filtro de período | Must | |
| Control de permisos | Must | |
| Protección adicional (contraseña de archivo) | Could | |

## Resumen de corte MVP recomendado

**Dentro (Must):** identidad básica, cuentas personales, movimientos (ingreso/gasto/transferencia), categorías, dashboard personal mínimo, grupos, gastos compartidos con dos modos de reparto, balances, liquidaciones, exportación CSV personal, autorización que impida ver datos de otros usuarios.

**Importante aplazable (Should):** presupuestos, deudas personales, recurrentes, historial detallado, exportación grupal, comparación de períodos, evolución de saldo.

**Futuro (Could/Won't MVP):** metas, alertas, Excel/PDF, conciliación, multi-moneda, intereses, open banking, cuentas compartidas, recordatorios push, roles complejos.

## Límites del sistema (propuestos)

- Un único producto web; no se define app nativa.
- No hay integraciones bancarias en el MVP.
- No hay módulo fiscal.
- No se afirma multi-tenant B2B (empresas como clientes) hasta que se pida.

## Dependencias de decisión antes de implementar

Ver [preguntas abiertas](../requirements/open-questions.md) y [riesgos](../delivery/risks-and-dependencies.md). Las bloqueantes para empezar código son Q-001, Q-002, Q-007, Q-011, Q-014, Q-018 y la aprobación del stack (DEC-001).
