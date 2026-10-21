# Consideraciones de privacidad

**Estado:** inventario y riesgos **propuestos**. **No** constituye política de privacidad, aviso legal ni declaración de cumplimiento.

Jurisdicción: **no determinada** (Q-018). No se afirma adecuación a GDPR, LFPDPPP, CCPA u otras.

## Datos personales previstos

| Dato | Finalidad propuesta | Notas |
| --- | --- | --- |
| Email | Login, invitaciones, reset | PII |
| Password hash | Autenticación | Secreto |
| Nombre visible | Mostrar en grupos | Puede ser seudónimo |
| Movimientos y cuentas | Servicio | Datos financieros sensibles |
| Membresías | Servicio de grupos | Relación entre personas |
| Tokens hash | Invitación / reset | |
| Logs técnicos | Seguridad y debug | Minimizar |
| Export CSV | Portabilidad informal | El usuario piloto descarga su copia |

No hay base legal definida (contrato, consentimiento, etc.) hasta Q-018.

## Datos que el MVP **no** debería recoger

- PAN de tarjetas, CVV, PIN.
- Documentos de identidad, selfie KYC.
- Geolocalización continua.
- Contactos del teléfono en bloque.

Si el producto los pide más adelante, hay que reabrir este documento y el modelo de amenazas.

## Visibilidad entre personas

Recomendación Q-013: los miembros ven actividad **del grupo**, no el libro personal ajeno.

Riesgo residual: un grupo familiar ve quién pagó qué; es inherente a la función. El usuario debe entenderlo al unirse (texto de UI pendiente, no redactado como legal).

## Exportaciones

- Solo datos autorizados.
- El CSV en el dispositivo del usuario queda fuera de control de MeldCash.
- Informar de ello en la UI (propuesto), no como cláusula legal definitiva.

## Conservación

Sin plazos (Q-019). Hechos financieros se conservan mientras la cuenta exista, para integridad.

## Derechos de las personas

No se implementan flujos de “acceso/rectificación/supresión/portabilidad” legales hasta Q-018. El CSV Must cubre una portabilidad **técnica** limitada de movimientos personales.

## Encargados

SMTP y hosting serían encargados de tratamiento si Q-018 aplica. No hay proveedores elegidos.

## Transferencias internacionales

Dependen de la región del hosting. No definidas.

## Menores

El brief no define edad mínima. **Pendiente:** no se recomienda un piloto con menores sin decisión explícita.

## Recomendación operativa

Antes de un piloto con datos reales:

1. Responder Q-018 y Q-019.
2. Redactar una política breve revisada por quien corresponda (no este análisis).
3. Configurar HTTPS y backups restringidos.
4. Evitar datos de producción en entornos de desarrollo.
