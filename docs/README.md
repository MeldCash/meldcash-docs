# Documentación de MeldCash

Estado de esta carpeta: **propuesta de análisis y diseño**, pendiente de aprobación del propietario del producto.

No hay implementación de producto en este repositorio. Nada de lo descrito aquí debe interpretarse como código existente, funcionalidad probada o decisión de negocio ya cerrada, salvo cuando se indique **confirmado** y se cite la fuente.

## Cómo leer esta documentación

1. [Inspección del repositorio](inspection/repository-diagnosis.md)
2. [Visión](product/vision.md), [alcance](product/scope.md) y [glosario](product/glossary.md)
3. Requisitos, reglas, historias, casos de uso y [preguntas abiertas](requirements/open-questions.md)
4. Tecnología, arquitectura y modelo de datos
5. Contratos de API, seguridad y pruebas
6. [Hoja de ruta del MVP](delivery/mvp-roadmap.md) y [Definition of Done](delivery/definition-of-done.md)
7. [Matriz de trazabilidad](requirements/traceability.md)

## Convención de estados

| Estado | Significado |
| --- | --- |
| Confirmado | Consta en el brief del propietario o en el repositorio, con fuente citada. |
| Propuesto | Recomendación del equipo de análisis; requiere aprobación. |
| Pendiente | Falta información o decisión; no debe implementarse como si estuviera cerrada. |
| Contradictorio | Hay evidencias incompatibles; se registra y se espera resolución. |
| No encontrado | Se buscó y no existe en el repositorio. |

## Identificadores

| Prefijo | Uso |
| --- | --- |
| RF | Requisito funcional |
| RNF | Requisito no funcional |
| RN | Regla de negocio |
| CU | Caso de uso |
| US | Historia de usuario |
| AC | Criterio de aceptación (también embebido en historias) |
| DEC | Decisión de arquitectura |
| Q | Pregunta pendiente |
| THR | Amenaza |
| TS | Escenario de prueba |

## Índice

### Producto

- [Visión](product/vision.md)
- [Alcance](product/scope.md)
- [Glosario](product/glossary.md)

### Requisitos

- [Requisitos funcionales](requirements/functional-requirements.md)
- [Requisitos no funcionales](requirements/non-functional-requirements.md)
- [Reglas de negocio](requirements/business-rules.md)
- [Historias de usuario](requirements/user-stories.md)
- [Casos de uso](requirements/use-cases.md)
- [Preguntas abiertas](requirements/open-questions.md)
- [Trazabilidad](requirements/traceability.md)

### Arquitectura

- [Visión general](architecture/architecture-overview.md)
- [Diagramas](architecture/diagrams.md)
- [Decisiones de arquitectura](architecture/architecture-decisions.md)
- [Evaluación tecnológica](architecture/technology-evaluation.md)

### Datos

- [Modelo de datos](data/data-model.md)
- [Diagrama entidad-relación](data/entity-relationship.md)
- [Diccionario de datos](data/data-dictionary.md)

### API

- [Visión general de API](api/api-overview.md)
- [Contratos](api/api-contracts.md)
- [Errores](api/error-handling.md)

### Seguridad

- [Diseño de seguridad](security/security-design.md)
- [Modelo de amenazas](security/threat-model.md)
- [Privacidad](security/privacy-considerations.md)

### Pruebas

- [Estrategia de pruebas](testing/test-strategy.md)
- [Escenarios de aceptación](testing/acceptance-scenarios.md)

### Entrega

- [Hoja de ruta del MVP](delivery/mvp-roadmap.md)
- [Riesgos y dependencias](delivery/risks-and-dependencies.md)
- [Definition of Done](delivery/definition-of-done.md)

## Qué no cubre esta fase

- Código de implementación, migraciones ejecutables o endpoints reales.
- Estimaciones en fechas o horas-persona (no hay datos de equipo).
- Afirmación de cumplimiento legal o de certificaciones.
- Pruebas ejecutadas: solo se documenta la estrategia.

## Próximo paso

El propietario del producto debe revisar las decisiones marcadas como **propuestas** y las preguntas `Q-*` antes de autorizar implementación.
