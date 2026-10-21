# Diagramas

**Estado:** propuestos. Sintaxis Mermaid para renderizar en visores compatibles.

## Contexto del sistema (C4-1)

```mermaid
C4Context
    title MeldCash — diagrama de contexto (propuesto)
    Person(user, "Usuario", "Finanzas personales y grupos")
    System(meld, "MeldCash", "SPA + API + PostgreSQL")
    System_Ext(smtp, "Proveedor de email", "Opcional: reset e invitaciones")
    Rel(user, meld, "Usa HTTPS")
    Rel(meld, smtp, "Envía correo si se aprueba Q-011/Q-017")
```

Si el render C4 no está disponible:

```mermaid
flowchart LR
    U[Usuario] -->|HTTPS| SPA[SPA MeldCash]
    SPA --> API[API REST]
    API --> DB[(PostgreSQL)]
    API -.->|pendiente| SMTP[Proveedor email]
```

## Contenedores (C4-2)

```mermaid
flowchart TB
    subgraph Cliente
      SPA[SPA React TypeScript]
    end
    subgraph Servidor
      API[API Spring Boot monolítica]
    end
    subgraph Datos
      PG[(PostgreSQL)]
    end
    subgraph Opcional
      SMTP[SMTP]
    end
    SPA -->|JSON /api/v1 cookie sesión| API
    API --> PG
    API -.-> SMTP
```

## Componentes de la API

```mermaid
flowchart TB
    subgraph API["API Spring Boot (un proceso)"]
      ID[Identity]
      AC[Accounts]
      LE[Ledger]
      GR[Groups]
      PL[Planning]
      DE[Debts]
      RP[Reporting]
      EX[Export]
      AU[Audit]
    end
    ID --> AC
    AC --> LE
    LE --> RP
    GR --> RP
    PL --> LE
    DE --> LE
    RP --> EX
    LE --> AU
    GR --> AU
```

`Groups` no depende de `Ledger` en el MVP (DEC-009, Q-002).

## Flujo: transferencia entre cuentas

```mermaid
sequenceDiagram
    actor U as Usuario
    participant SPA
    participant API as Ledger
    participant DB as PostgreSQL
    U->>SPA: Confirma transferencia
    SPA->>API: POST /transfers + Idempotency-Key
    API->>DB: BEGIN
    API->>DB: Insertar TRANSFER_OUT y TRANSFER_IN
    API->>DB: Actualizar cached_balance origen y destino
    API->>DB: Guardar idempotencia
    API->>DB: COMMIT
    API-->>SPA: 201 Transfer
    SPA-->>U: Saldos actualizados
```

## Flujo: gasto compartido y liquidación

```mermaid
sequenceDiagram
    actor A as Miembro A
    actor B as Miembro B
    participant API as Groups
    A->>API: POST /groups/{id}/shared-expenses
    API-->>A: 201 gasto + splits
    B->>API: GET /groups/{id}/balances
    API-->>B: B debe a A
    B->>API: POST /groups/{id}/settlements
    API-->>B: 201 liquidación
    Note over API: No se crean movimientos en Ledger
```

## Autorización

```mermaid
flowchart TD
    R[Request] --> AUTH{¿Sesión válida?}
    AUTH -->|No| E401[401]
    AUTH -->|Sí| T{¿Tipo de recurso?}
    T -->|Personal| OWN{¿user_id = sesión?}
    T -->|Grupo| MEM{¿membership ACTIVE?}
    OWN -->|No| E403[403]
    OWN -->|Sí| OK[Ejecutar caso de uso]
    MEM -->|No| E403
    MEM -->|Sí| OK
```
