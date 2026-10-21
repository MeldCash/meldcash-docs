# Requisitos no funcionales

**Estado:** todos **propuestos**, salvo donde se cite el brief. No hay métricas de negocio ni de equipo confirmadas; los umbrales numéricos son **provisionales** para orientar el diseño del MVP, no SLAs contractuales.

## Calidad de datos y consistencia

| ID | Nombre | Descripción | Justificación | Pri | Estado |
| --- | --- | --- | --- | --- | --- |
| RNF-001 | Integridad transaccional | Las operaciones que afectan más de un registro financiero (transferencia, gasto compartido + splits, liquidación) se confirman o se revierten como una unidad. | Evitar saldos a medias. | M | Propuesto |
| RNF-002 | Precisión monetaria | Los importes se almacenan y calculan con tipo decimal exacto, no `float`/`double` binario. | Brief de modelo de datos; finanzas. | M | Propuesto |
| RNF-003 | Idempotencia de escritura | Creación de movimientos, gastos compartidos y liquidaciones acepta una clave de idempotencia de cliente para reintentos de red. | Evitar duplicados. | M | Propuesto |
| RNF-004 | Concurrencia | Dos actualizaciones simultáneas del mismo agregado no deben corromper saldos; se usa control optimista o equivalente. | Operación concurrente. | M | Propuesto |

## Seguridad (ver también `security/`)

| ID | Nombre | Descripción | Pri | Estado |
| --- | --- | --- | --- | --- |
| RNF-010 | Autenticación fuerte de sesión | Credenciales y sesión/token según DEC-003; no almacenar contraseñas en claro. | M | Propuesto |
| RNF-011 | Autorización por objeto | Toda lectura/escritura comprueba titularidad personal o membresía de grupo. | M | Propuesto |
| RNF-012 | Cifrado en tránsito | HTTPS/TLS en cualquier despliegue no local. | M | Propuesto |
| RNF-013 | Protección de exportaciones | La exportación aplica los mismos controles que la consulta; no se generan archivos de datos ajenos. | M | Propuesto |
| RNF-014 | Secretos | Secretos fuera del código; no se documentan valores reales. | M | Propuesto |
| RNF-015 | Cabeceras y controles web | Protección OWASP de baseline (CSRF si cookie, XSS, clickjacking). | M | Propuesto |

## Usabilidad y accesibilidad

| ID | Nombre | Descripción | Pri | Estado |
| --- | --- | --- | --- | --- |
| RNF-020 | Web adaptable | La UI es usable en viewport de escritorio y móvil navegador. | M | Confirmado el requisito de plataforma web adaptable (brief); el umbral de breakpoints es propuesto |
| RNF-021 | Claridad de espacios | La interfaz etiqueta de forma persistente si la vista es personal o de un grupo concreto. | M | Propuesto |
| RNF-022 | Accesibilidad | Objetivo WCAG 2.2 nivel AA para flujos Must del MVP. Umbral pendiente de aprobación. | S | Pendiente |
| RNF-023 | Idioma de UI | Pendiente (Q-027). La documentación es en español. | — | Pendiente |

## Rendimiento y capacidad (umbrales provisionales)

No hay perfiles de carga confirmados. Propuesta para un piloto pequeño:

| ID | Nombre | Umbral provisional | Pri | Estado |
| --- | --- | --- | --- | --- |
| RNF-030 | Latencia de API de consulta | p95 < 500 ms en entorno de piloto, listas paginadas de 50 ítems | S | Propuesto |
| RNF-031 | Tamaño de piloto | Diseñar para cientos de usuarios y decenas de miles de movimientos, no para millones | S | Propuesto |
| RNF-032 | Exportación CSV | Hasta 10 000 filas de forma síncrona; por encima, asíncrono (no Must) | C | Propuesto |

## Observabilidad

| ID | Nombre | Descripción | Pri | Estado |
| --- | --- | --- | --- | --- |
| RNF-040 | Trazas de correlación | Cada request lleva un identificador de correlación en logs. | S | Propuesto |
| RNF-041 | Logs sin secretos | No registrar contraseñas, tokens ni IBAN completos. | M | Propuesto |
| RNF-042 | Métricas de salud | Health check del API y de la base de datos. | S | Propuesto |

## Operación y entrega

| ID | Nombre | Descripción | Pri | Estado |
| --- | --- | --- | --- | --- |
| RNF-050 | Entorno reproducible | Desarrollo local con un único compose o equivalente, tras aprobar stack. | S | Propuesto |
| RNF-051 | Migraciones versionadas | Esquema de BD evoluciona con migraciones revisables. | M | Propuesto |
| RNF-052 | Copias de seguridad | Política de backup del entorno de piloto, pendiente de infra. | M | Pendiente |
| RNF-053 | Tiempo de retención de logs | Pendiente (Q-019). | — | Pendiente |

## Cumplimiento

| ID | Nombre | Descripción | Estado |
| --- | --- | --- | --- |
| RNF-060 | Marco legal aplicable | No se afirma GDPR, LFPDPPP u otra norma hasta definir jurisdicción (Q-018). | Pendiente |

Cualquier requisito de cumplimiento futuro se añadirá con identificador nuevo, no se marcará RNF-060 como cumplido por existir este documento.
