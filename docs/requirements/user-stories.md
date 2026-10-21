# Historias de usuario

**Estado:** **propuestas**. Formato: como [perfil], quiero [acción] para [beneficio].  
Prioridad MoSCoW: propuesta de alcance. Criterios de aceptación (AC) verificables.

Los perfiles no son roles de autorización definitivos.

## Identidad

### US-001 — Registro e inicio de sesión

**Como** persona nueva, **quiero** crear una cuenta e iniciar sesión **para** usar MeldCash.

- Prioridad: Must
- Requisitos: RF-001, RF-002
- Estado: pendiente de Q-011, Q-014

**AC-001** Dado un correo no registrado y una contraseña que cumple la política aprobada, cuando envío el alta, entonces queda una cuenta usable e inicio sesión.  
**AC-002** Dado un correo ya registrado, cuando intento el alta, entonces se rechaza sin revelar más datos de los necesarios (detalle de mensaje: Q-014).  
**AC-003** Dadas credenciales inválidas, cuando inicio sesión, entonces se rechaza con el mismo tipo de error genérico.

## Espacio personal

### US-010 — Alta de cuentas y saldo inicial

**Como** usuario individual, **quiero** crear cuentas de efectivo y banco con saldo inicial **para** saber dónde está mi dinero.

- Prioridad: Must — RF-010, RF-011 — RN-008, RN-010

**AC-010** Dado que creo “Efectivo” con saldo inicial 100.00, cuando consulto la cuenta sin movimientos, entonces el saldo es 100.00.  
**AC-011** No puedo ver ni editar cuentas de otro usuario.

### US-020 — Registrar ingreso

**Como** usuario individual, **quiero** registrar un ingreso **para** reflejar dinero que entra.

- Prioridad: Must — RF-020 — RN-008, RN-020

**AC-020** Dada una cuenta con saldo 100.00, cuando registro un ingreso de 50.00 en esa cuenta, entonces el saldo pasa a 150.00 y el tipo es ingreso.  
**AC-021** Un importe 0 o negativo se rechaza.

### US-021 — Registrar gasto

**Como** usuario individual, **quiero** registrar un gasto **para** reflejar dinero que sale.

- Prioridad: Must — RF-021

**AC-022** Dada una cuenta con saldo 100.00, cuando registro un gasto de 30.00, entonces el saldo es 70.00 y cuenta en gastos del período.  
**AC-023** El gasto no aparece en ingresos ni como transferencia.

### US-022 — Transferir entre cuentas

**Como** usuario individual, **quiero** transferir entre mis cuentas **para** no perder de vista el dinero sin inflar ingresos o gastos.

- Prioridad: Must — RF-022 — RN-014, RN-022

**AC-024** Dadas cuenta A=100 y B=20, cuando transfiero 40 de A a B, entonces A=60, B=60, ingresos del período no cambian, gastos del período no cambian.  
**AC-025** No puedo transferir a una cuenta que no es mía.  
**AC-026** Reintentar el mismo `Idempotency-Key` no crea una segunda transferencia.

### US-023 — Anular un movimiento

**Como** usuario, **quiero** anular un movimiento erróneo **para** corregir sin borrar la historia.

- Prioridad: Must — RF-024 — RN-012

**AC-027** Al anular el gasto de 30.00 del AC-022, el saldo vuelve al valor previo y el gasto deja de sumar en el período.  
**AC-028** El movimiento anulado sigue listándose con estado `VOID`.

### US-040 — Ver resumen del período

**Como** usuario, **quiero** ver ingresos, gastos, resultado y gastos por categoría **para** entender el período.

- Prioridad: Must — RF-040, RF-042

**AC-040** Un ingreso 100, un gasto 40 y una transferencia 25 en el mismo mes muestran ingresos 100, gastos 40, resultado 60.  
**AC-041** La distribución por categoría solo usa los gastos no anulados.

### US-050 — Presupuesto (Should)

**Como** usuario, **quiero** fijar un presupuesto de categoría **para** comparar con el gasto real.

- Prioridad: Should — RF-050, RF-051 — RN-030

**AC-050** Presupuesto “comida” 200 en octubre; gastos comida 50 y 30: consumido 80, restante 120.  
**AC-051** Una transferencia no aumenta el consumido.

### US-060 — Deuda personal (Should)

**Como** usuario, **quiero** registrar que le debo a alguien y pagar parcialmente **para** no confundirlo con un gasto de grupo.

- Prioridad: Should — RF-060, RF-061 — RN-045, RN-047

**AC-060** Deuda original 100, pago 40: pendiente 60.  
**AC-061** Pago 70 sobre pendiente 60 se rechaza.  
**AC-062** La deuda no aparece como balance de ningún grupo salvo que el usuario la haya creado aparte.

### US-070 — Recurrente (Should)

**Como** usuario, **quiero** registrar el alquiler mensual **sin** que se marque pagado solo por llegar el día.

- Prioridad: Should — RF-070–RF-073 — RN-040, RN-044

**AC-070** El día de la ocurrencia el estado sigue `PENDING` hasta que confirmo.  
**AC-071** Al confirmar se crea un gasto vinculado y la ocurrencia pasa a `PAID`.  
**AC-072** Omitir no crea movimiento.

### US-080 — Exportar CSV personal

**Como** usuario, **quiero** exportar mis movimientos filtrados **para** revisarlos fuera de la app.

- Prioridad: Must — RF-080 — RN-052

**AC-080** El CSV contiene solo mis movimientos no ajenos, respetando el filtro de fechas.  
**AC-081** Un usuario autenticado no obtiene CSV de otro usuario cambiando un id.

## Grupos

### US-030 — Crear grupo e invitar

**Como** propietario de grupo (perfil de análisis), **quiero** crear un grupo e incorporar miembros **para** compartir gastos.

- Prioridad: Must — RF-030, RF-031
- Depende: Q-017

**AC-030** Tras crear el grupo soy miembro con capacidad de administración propuesta.  
**AC-031** Un invitado que acepta aparece como miembro `ACTIVE`.  
**AC-032** Un no miembro no lista el grupo.

### US-031 — Gasto compartido a partes iguales

**Como** miembro, **quiero** registrar un gasto pagado por mí y repartido a partes iguales **para** saber qué me deben.

- Prioridad: Must — RF-034, RF-035 — RN-035, RN-038

**AC-033** A paga 90.00 entre A, B y C a partes iguales: splits 30.00 cada uno (90 divisible). Balance: B debe 30 a A, C debe 30 a A, A no se debe a sí mismo.  
**AC-034** El dashboard personal de A no aumenta gastos en 90.00 por este hecho (RN-023).  
**AC-035** B no puede registrar el mismo hecho como otro gasto compartido “porque también participó” sin una acción nueva; el sistema no duplica en automático.

### US-032 — Reparto con resto de centavos

**Como** miembro, **quiero** que 10.00 entre 3 personas sume exactamente 10.00.

- Prioridad: Must — RN-035

**AC-036** Splits resultantes suman 10.00. El remanente de 0.01 se asigna según RN-035 de forma determinista en pruebas repetidas.

### US-033 — Importes personalizados

**Como** miembro, **quiero** asignar importes distintos **para** reflejar consumo desigual.

- Prioridad: Must — RF-036 — RN-036

**AC-037** Total 100; splits 50, 30, 20: se acepta.  
**AC-038** Splits 50, 30, 19: se rechaza.

### US-034 — Liquidación

**Como** miembro, **quiero** registrar que le pagué a quien correspondía **para** reducir el pendiente sin crear un gasto nuevo.

- Prioridad: Must — RF-038 — RN-039

**AC-039** Tras AC-033, B liquida 30 a A: balance B–A queda 0; no hay gasto compartido adicional; gastos personales de B no aumentan automáticamente.  
**AC-040** Liquidación de 0 o negativa se rechaza.

### US-035 — Resumen de grupo

**Como** miembro, **quiero** ver quién debe a quién **para** entender la caja común.

- Prioridad: Must — RF-037, RF-045

**AC-041** La vista de grupo muestra balances derivados, no saldos de cuentas bancarias personales de los miembros.
