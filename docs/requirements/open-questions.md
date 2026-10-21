# Preguntas abiertas

**Estado de todas:** pendiente de aprobación del **propietario del producto**, salvo que se designe otro responsable.  
No bloquean la redacción de propuestas, pero sí deben resolverse antes de implementar el área afectada.

Cada ítem incluye alternativas, recomendación **provisional** y el impacto de no decidir.

## Q-001 — ¿Una cuenta financiera pertenece solo a una persona o puede ser compartida?

- **Por qué importa:** Define el modelo de saldos, transferencias y autorización.
- **Alternativas:**
  1. Solo personales (1 usuario : N cuentas).
  2. Cuentas de grupo con varios titulares.
  3. Híbrido.
- **Pros/contras:** (1) simple, evita mezclar caja común con banco real; no cubre “cuenta conjunta”. (2) realista para parejas, complica permisos y saldos. (3) máximo alcance, máximo riesgo de confusión.
- **Recomendación provisional:** (1) en MVP. La caja común se modela con **gastos compartidos y balances**, no con una cuenta bancaria compartida.
- **Impacto si no se resuelve:** El modelo de datos y la API de cuentas no pueden cerrarse.
- **Bloquea:** RF-010, RN-010, DEC-006

## Q-002 — ¿Cómo se separan y, si procede, se reflejan los movimientos personales y grupales?

- **Por qué importa:** Es el riesgo principal de duplicación.
- **Alternativas:**
  1. Espacios estrictamente separados; el gasto compartido nunca crea movimiento personal.
  2. Al guardar un gasto compartido, el pagador puede opcionalmente crear un gasto personal por el total.
  3. Cada participante crea automáticamente un gasto personal por su split.
- **Pros/contras:** (1) cero duplicación automática; el usuario no ve “su parte” en el presupuesto personal. (2) refleja la salida de caja del pagador; hay que etiquetar el origen para no doble-contar en informes combinados. (3) duplica el hecho N veces; **no recomendado**.
- **Recomendación provisional:** (1) para MVP; (2) como Should posterior, siempre explícito y trazable al `shared_expense_id`.
- **Impacto:** Informes, presupuestos (RN-023, RN-032), UI.
- **Bloquea:** RN-023, dashboard combinado

## Q-003 — ¿Cómo se calculan los saldos compartidos?

- **Por qué importa:** Distintos algoritmos (deuda bruta vs simplificación) cambian lo que ve el usuario.
- **Alternativas:**
  1. Balance por pares sin simplificar ciclos (A→B, B→C, C→A se muestran).
  2. Simplificación de deudas (mínimo de pagos).
- **Recomendación provisional:** (1) en MVP por transparencia; (2) Could.
- **Impacto:** RF-037, tests de liquidación.

## Q-004 — ¿Cómo se registran las liquidaciones respecto a cuentas personales?

- **Por qué importa:** Una liquidación es un pago real que puede salir de una cuenta.
- **Alternativas:**
  1. Solo registro grupal (quién pagó a quién).
  2. Registro grupal + movimiento personal opcional (transferencia o gasto).
  3. Siempre movimiento personal.
- **Recomendación provisional:** (1) en MVP; (2) después, explícito.
- **Impacto:** Duplicación de gastos; conciliación mental del usuario.

## Q-005 — ¿Qué ocurre cuando un miembro abandona un grupo?

- **Por qué importa:** Saldos abiertos e historial.
- **Alternativas:**
  1. Bloquear salida si hay balance ≠ 0.
  2. Permitir salida y conservar historial de solo lectura; no participa en gastos nuevos.
  3. Reasignar o condonar saldos.
- **Recomendación provisional:** (1) + historial conservado; condonación como acción explícita de admin.
- **Impacto:** RF-033, retención de datos.

## Q-006 — ¿Quién puede editar o anular un gasto compartido o una liquidación?

- **Alternativas:** solo el autor; autor o admin; cualquier miembro; nadie tras N días.
- **Recomendación provisional:** autor o administrador del grupo, mientras no esté anulado; ventana temporal no definida (no inventar).
- **Impacto:** RF-039, auditoría, disputas familiares.

## Q-007 — ¿Qué monedas se permiten y hay conversión?

- **Alternativas:** una moneda por instalación; una por usuario; una por cuenta y por grupo sin FX; FX con tasas.
- **Recomendación provisional:** una moneda por cuenta y una por grupo; **sin conversión** en MVP. Moneda por defecto pendiente (no asumir USD ni local).
- **Impacto:** todos los importes, informes, exportaciones.

## Q-008 — ¿Se pueden corregir operaciones históricas de períodos “cerrados”?

- Relacionada con Q-009. ¿Hay cierre de mes?
- **Recomendación provisional:** no hay cierre contable en MVP; sí anulación/edición controlada con recálculo.
- **Impacto:** presupuestos e informes ya vistos por el usuario.

## Q-009 — ¿Qué campos de un movimiento histórico se pueden editar?

- **Alternativas:** todos; solo descripción/categoría; cualquier campo con recálculo; solo anular y crear nuevo.
- **Recomendación provisional:** descripción y categoría editables; importe, cuenta, fecha y tipo: o bien edición con recálculo, o anular+crear (más auditable). Preferir **anular+crear** para importe/cuenta/tipo.
- **Impacto:** RF-023, auditoría.

## Q-010 — ¿Qué significa “saldo disponible”?

- **Alternativas:** saldo contable RN-008; saldo menos presupuestos restantes; saldo menos deudas; saldo sin descubierto.
- **Recomendación provisional:** en MVP mostrar **saldo de cuenta** (RN-008) etiquetado como “saldo”, no “disponible”, hasta definir disponibilidad.
- **Impacto:** copy de UI, RN-009.

## Q-011 — ¿Autenticación y recuperación?

- **Alternativas:** email+password + reset por email; OAuth (Google); passkeys; MFA.
- **Recomendación provisional:** email+password + reset por email en MVP; MFA Could; OAuth Could. Requiere **capacidad de envío de email** (dependencia).
- **Impacto:** RF-001–RF-004, seguridad, infra.

## Q-012 — (reservado) Datos visibles — ver Q-013.

## Q-013 — ¿Qué datos de un grupo puede ver y exportar cada miembro?

- **Alternativas:** todo el historial del grupo; solo gastos donde participa; admin ve todo, miembro ve lo suyo.
- **Recomendación provisional:** todo miembro activo ve gastos, splits y liquidaciones del grupo (transparencia familiar). No ve cuentas ni movimientos **personales** de otros.
- **Impacto:** RF-081, privacidad, IDOR.

## Q-014 — ¿Datos mínimos de registro y verificación de email?

- **Alternativas:** email+password+nombre; +verificación obligatoria; teléfono.
- **Recomendación provisional:** email, password, nombre visible; verificación de email Should (puede aplazar el piloto si no hay SMTP).
- **Impacto:** RF-001, spam, invitaciones.

## Q-015 — ¿Zona horaria de fechas e informes?

- **Alternativas:** UTC only; TZ del usuario; TZ del grupo; fecha civil sin hora.
- **Recomendación provisional:** `fecha_valor` como date civil; `created_at` timestamptz UTC; TZ de display en perfil (default pendiente).
- **Impacto:** RN-054, comparaciones de período.

## Q-016 — ¿Cuentas y deudas de usuarios eliminados?

- **Alternativas:** borrado lógico; anonimizar; bloqueo de borrado si hay grupos; retención N días.
- **Recomendación provisional:** no ofrecer borrado de cuenta en MVP; si se exige, borrado lógico y anonimización pendiente de Q-018.
- **Impacto:** RN-053, privacidad.

## Q-017 — ¿Mecanismo de invitación a grupos?

- **Alternativas:** email; enlace con token; código de grupo; solo usuarios ya registrados.
- **Recomendación provisional:** invitar por email o identificador de usuario ya registrado + enlace con token de un solo uso y caducidad. Detalle de caducidad a aprobar (no inventar horas).
- **Riesgos:** THR-009.
- **Impacto:** RF-031, seguridad.

## Q-018 — ¿Jurisdicción y base legal de tratamiento de datos?

- **Recomendación provisional:** no afirmar cumplimiento. Documentar inventario (privacy-considerations.md) y pedir al propietario país de operación y de usuarios piloto.
- **Impacto:** privacidad, retención, cláusulas.

## Q-019 — ¿Retención de backups, logs y exportaciones?

- Sin política no se implementan borrados automáticos.
- **Recomendación:** definir mínimo para piloto (por ejemplo retención de backups) **después** de Q-018. No se fija plazo aquí.

## Q-020 — ¿Categorías: catálogo global, por usuario, por grupo?

- **Recomendación provisional:** catálogo base del sistema + categorías personales. Grupos usan un subconjunto o texto libre de categoría de gasto compartido (campo simple en MVP).
- **Impacto:** RF-027, presupuestos.

## Q-021 — ¿Cómo se recogerá feedback del MVP?

- Entrevistas, formulario, issue tracker: no está en el brief.
- **Impacto:** criterios de salida OBJ-005.

## Q-022 — ¿Tipos de cuenta más allá de efectivo y banco?

- Tarjeta de crédito (pasivo), monedero, ahorro: Could.
- **Recomendación MVP:** `CASH`, `BANK`. Tarjeta implica modelo de pasivo distinto; no asumirla.

## Q-023 — ¿Una deuda personal puede vincularse a un grupo o a un usuario de MeldCash?

- **Recomendación:** en MVP, deuda con **nombre libre** (contraparte no necesariamente usuario). Vínculo a usuario/grupo Could.

## Q-024 — ¿La categoría es obligatoria en un movimiento?

- **Recomendación:** obligatoria para gastos si hay presupuestos; opcional en ingresos en MVP temprano. Hasta aprobar: **obligatoria en gastos**, opcional en ingresos, categoría `SIN_CATEGORIA` de sistema si se permite vacío.

## Q-025 — ¿Un pago de deuda crea movimiento de cuenta?

- **Recomendación:** checkbox explícito “registrar también como gasto/transferencia”; default no en MVP Should.

## Q-026 — ¿Cambiar una regla recurrente altera ocurrencias pasadas?

- **Recomendación:** solo futuras `PENDING`; las `PAID` no se tocan.

## Q-027 — ¿Idioma de la interfaz?

- Brief exige docs en español, no el idioma de UI.
- **Recomendación:** español para MVP si el piloto es hispanohablante; no asumir i18n completa.

## Q-028 — ¿Se puede anular un gasto compartido si ya hay liquidaciones?

- **Recomendación:** sí, con recálculo; liquidaciones quedan. Advertir en UI. Alternativa: exigir anular liquidaciones primero (más rígido).

## Q-029 — ¿Nombre visible vs identificador en un grupo?

- Privacidad entre miembros (email visible o no).
- **Recomendación:** mostrar nombre de perfil, no email, en listados; el admin puede ver el email del invitado pendiente.

## Decisiones bloqueantes para empezar a programar

Must resolverse o aceptar explícitamente la recomendación provisional:

Q-001, Q-002, Q-007, Q-011, Q-014, Q-017, Q-018, más aprobación de DEC-001 (stack) y del corte MoSCoW en `scope.md`.
