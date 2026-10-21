# Visión del producto

**Estado:** visión **confirmada** en sus enunciados tomados del brief; detalle operativo **propuesto**.  
**Fuente confirmada:** brief del propietario del proyecto, fase de análisis previo al desarrollo.

## Problema

Las personas necesitan registrar y comprender sus movimientos financieros, y a la vez compartir gastos con familiares o grupos, sin mezclar el dinero propio con el compartido ni duplicar ingresos, gastos, deudas o transferencias.

## Concepto confirmado

**MeldCash** es una plataforma **web adaptable** para:

- gestionar finanzas personales;
- compartir gastos entre familiares o grupos.

## Objetivo general confirmado

Diseñar una aplicación que permita a sus usuarios:

- registrar y comprender movimientos financieros;
- organizar presupuestos;
- administrar cuentas;
- controlar deudas y gastos compartidos;
- consultar informes;
- exportar información.

El sistema debe facilitar la comprensión de las finanzas **individuales** y **grupales** sin confundir dinero propio con dinero compartido y sin duplicar hechos económicos.

## Objetivo del alcance inicial (confirmado)

Un **MVP validable**: demostrar funcionalidades principales y recoger comentarios de usuarios antes de ampliar el producto.

La tecnología **no está decidida**. La recomendación está en [evaluación tecnológica](../architecture/technology-evaluation.md) (propuesta).

## Objetivos de producto (propuestos, para validar el MVP)

Estos objetivos son medibles a nivel de producto, no KPIs de negocio inventados. Requieren aprobación.

| ID | Objetivo | Cómo se validaría |
| --- | --- | --- |
| OBJ-001 | Un usuario puede registrar ingresos, gastos y transferencias sin que las transferencias inflen ingresos o gastos. | Escenarios TS-001 a TS-003. |
| OBJ-002 | Un usuario distingue su espacio personal del espacio de cada grupo. | Informes y listados no mezclan totales salvo vistas explícitamente combinadas (si se aprueban). |
| OBJ-003 | Un grupo puede registrar un gasto compartido, ver quién debe a quién y registrar una liquidación sin duplicar el gasto. | Escenarios TS-004 a TS-006. |
| OBJ-004 | Un usuario puede consultar un resumen básico y exportar un extracto personal en CSV. | TS-007, TS-010. |
| OBJ-005 | El equipo puede recoger feedback de usuarios piloto sobre claridad de saldos y gastos compartidos. | Criterio de salida del MVP; método de recogida pendiente (Q-021). |

## No-objetivos (propuestos para el MVP)

No forman parte del objetivo inicial salvo aprobación explícita:

- agregación bancaria / open banking;
- inversión, cripto o contabilidad fiscal completa;
- intereses, recargos y calendarios automáticos de deuda;
- aplicación nativa iOS/Android (sí web adaptable);
- multi-moneda con conversión en vivo;
- cumplimiento declarado de normas concretas (PCI, SOC 2, ISO 27001, GDPR como afirmación legal) sin jurisdicción definida.

## Usuarios previstos (perfiles de análisis, no roles definitivos)

Fuente: brief. Permisos: ver requisitos RF-030+ y preguntas Q-006, Q-013.

| Perfil | Necesidad principal | Notas |
| --- | --- | --- |
| Usuario individual | Registrar y entender su dinero | Puede no usar grupos. |
| Miembro de familia o grupo | Ver y registrar actividad compartida autorizada | Alcance de visibilidad pendiente (Q-013). |
| Propietario o administrador de grupo | Crear/administrar el grupo | Permisos no cerrados. |
| Usuario de movimientos compartidos | Registrar, consultar, liquidar | Puede coincidir con los anteriores. |

No se ha confirmado si existen roles distintos de “usuario autenticado” y “miembro de grupo”, ni si hay usuarios invitados sin cuenta.

## Principios de diseño funcional (propuestos)

1. **Un hecho económico, un registro canónico.** Las vistas se derivan; no se reintroducen copias como si fueran hechos nuevos.
2. **Separación de espacios.** Personal y grupal se consultan y exportan con reglas de autorización distintas.
3. **Las transferencias no son resultado.** No cuentan como ingreso ni como gasto.
4. **Lo vencido no está pagado.** Una fecha de una regla recurrente no crea un movimiento real.
5. **Trazabilidad.** Ediciones y eliminaciones controladas dejan rastro suficiente para entender el historial.
6. **Conservadurismo financiero.** Ante duda de duplicación, el sistema prefiere bloquear o exigir confirmación antes que doble conteo.

## Restricciones conocidas

| Restricción | Estado | Fuente |
| --- | --- | --- |
| No implementar producto en esta fase | Confirmado | Brief |
| Documentación en español técnico | Confirmado | Brief |
| Tecnología por decidir | Confirmado | Brief |
| Presupuesto, equipo y plazos | No encontrado | — |
| Jurisdicción y política de privacidad | Pendiente | Q-018, Q-019 |
