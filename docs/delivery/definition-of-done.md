# Definition of Done

Hay tres niveles. Esta fase solo cubre el primero.

## DoD de la fase de análisis (esta entrega)

Se considera cumplida si:

- [x] Repositorio inspeccionado y diagnosticado.
- [x] Visión y alcance escritos distinguiendo confirmado/propuesto.
- [x] Requisitos, reglas, historias y casos de uso identificados y priorizados (propuestos).
- [x] Preguntas y tensiones registradas.
- [x] Stack justificado, no impuesto.
- [x] Arquitectura y modelo de datos a nivel de diseño.
- [x] Flujos financieros con reglas y escenarios de aceptación.
- [x] Seguridad y privacidad consideradas sin fingir compliance.
- [x] Trazabilidad de requisitos principales.
- [x] Cambios limitados a documentación autorizada.
- [x] Informe final con aprobaciones pendientes.

No se exige: código, CI verde, ni aprobación ya firmada.

## DoD de una historia en implementación (propuesto, futuro)

Una US Must no se cierra hasta:

1. Contrato de API respetado o actualizado en docs.
2. Validaciones de servidor y constraints.
3. Transacción atómica si aplica.
4. Tests unitarios/API de los AC.
5. Caso de IDOR negativo.
6. UI adaptable del flujo, con etiqueta de espacio personal/grupo.
7. Sin secretos en logs.
8. Revisión humana.

## DoD del MVP piloto (propuesto, futuro)

1. TS-001–TS-007 y TS-010 automatizados en API como mínimo.
2. E2E smoke de esos flujos.
3. HTTPS en el entorno piloto.
4. Backup restaurable al menos una vez (ensayo).
5. Lista Won't intacta salvo change request escrito.
6. Feedback Q-021 con canal abierto.

**Hoy no se declara el MVP como hecho.**
