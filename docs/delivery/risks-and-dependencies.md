# Riesgos y dependencias

**Estado:** propuesto.

## Riesgos de producto

| ID | Riesgo | Impacto | Mitigación propuesta |
| --- | --- | --- | --- |
| RSK-001 | Confusión personal vs grupal | Alto (falla el concepto) | DEC-007, RN-023, copy de UI, pruebas de usabilidad |
| RSK-002 | Duplicación de gastos compartidos | Alto | Un registro canónico; no auto-copiar a ledger |
| RSK-003 | Alcance inflado (presupuestos+deudas+recurrentes+PDF) | Alto | MoSCoW; Should opcional |
| RSK-004 | Multi-moneda sin reglas | Alto | Q-007; una moneda por cuenta/grupo |
| RSK-005 | Invitaciones inseguras o sin email | Medio | Q-017; no improvisar tokens en query logs |
| RSK-006 | Ausencia de jurisdicción | Medio-Alto | No recoger más PII de la necesaria; Q-018 antes de piloto real |
| RSK-007 | Stack inadecuado al equipo | Alto | Reabrir DEC-001 |
| RSK-008 | Redondeo percibido como “injusto” | Medio | Documentar RN-035 en la UI |
| RSK-009 | Abandono de grupo con deudas | Medio | Q-005 |
| RSK-010 | Edición histórica que cambia informes ya vistos | Medio | Q-008/Q-009; anular+crear |
| RSK-011 | SMTP no disponible | Medio | Aplazar reset e invitaciones email; usuarios pre-creados en piloto |
| RSK-012 | Repo vacío malinterpretado como producto listo | Bajo | Este diagnóstico |

## Dependencias técnicas

- JDK 21, Node, PostgreSQL, Docker (si se aprueba el stack).
- Proveedor HTTPS y DNS.
- Proveedor email (condicional).
- GitHub Actions (condicional al remoto actual).

## Dependencias de producto

- Propietario disponible para cerrar Q-*.
- Usuarios piloto y canal de feedback (Q-021).
- Textos de UI e idioma (Q-027).
- Catálogo de categorías inicial (Q-020).

## Dependencias legales/ops

- Quién es responsable de backups y secretos.
- Edad mínima y si hay menores.
- Política de privacidad redactada por responsable competente.

## Contradicciones

Ninguna entre archivos del repositorio de código (no hay código).  
Posible tensión **de alcance** (no contradicción factual): el objetivo general lista presupuestos, deudas y exportaciones varias, mientras el MVP validable puede recortarlas a Should/Could. Queda registrada para que el propietario elija, no se oculta.
