# Diagrama entidad-relación

**Estado:** propuesto, alineado con [data-model.md](data-model.md).

```mermaid
erDiagram
    USER ||--o{ ACCOUNT : owns
    USER ||--o{ TRANSACTION : books
    USER ||--o{ GROUP_MEMBERSHIP : has
    USER ||--o{ GROUP : creates
    USER ||--o{ BUDGET : plans
    USER ||--o{ DEBT : records
    USER ||--o{ RECURRING_RULE : defines
    USER ||--o{ AUDIT_EVENT : acts

    GROUP ||--o{ GROUP_MEMBERSHIP : includes
    GROUP ||--o{ GROUP_INVITATION : issues
    GROUP ||--o{ SHARED_EXPENSE : has
    GROUP ||--o{ SETTLEMENT : has

    ACCOUNT ||--o{ TRANSACTION : holds
    CATEGORY ||--o{ TRANSACTION : classifies
    CATEGORY ||--o{ BUDGET : limits

    SHARED_EXPENSE ||--o{ SHARED_EXPENSE_SPLIT : splits
    DEBT ||--o{ DEBT_PAYMENT : receives
    RECURRING_RULE ||--o{ RECURRING_OCCURRENCE : generates
    RECURRING_OCCURRENCE |o--o| TRANSACTION : confirms
    DEBT_PAYMENT |o--o| TRANSACTION : optional

    USER {
        uuid id PK
        string email
        string password_hash
        string display_name
        string status
    }
    ACCOUNT {
        uuid id PK
        uuid user_id FK
        string name
        string type
        string currency_code
        numeric initial_balance
        numeric cached_balance
        bigint version
    }
    TRANSACTION {
        uuid id PK
        uuid user_id FK
        uuid account_id FK
        uuid category_id FK
        uuid transfer_id
        string type
        numeric amount
        date value_date
        string status
    }
    CATEGORY {
        uuid id PK
        uuid user_id FK
        string name
        string kind
    }
    GROUP {
        uuid id PK
        string name
        string currency_code
        uuid created_by FK
    }
    GROUP_MEMBERSHIP {
        uuid id PK
        uuid group_id FK
        uuid user_id FK
        string role
        string status
    }
    GROUP_INVITATION {
        uuid id PK
        uuid group_id FK
        string email
        string token_hash
        timestamptz expires_at
    }
    SHARED_EXPENSE {
        uuid id PK
        uuid group_id FK
        uuid payer_user_id FK
        numeric amount
        string split_mode
        string status
    }
    SHARED_EXPENSE_SPLIT {
        uuid id PK
        uuid shared_expense_id FK
        uuid participant_user_id FK
        numeric amount
    }
    SETTLEMENT {
        uuid id PK
        uuid group_id FK
        uuid from_user_id FK
        uuid to_user_id FK
        numeric amount
        string status
    }
    BUDGET {
        uuid id PK
        uuid user_id FK
        uuid category_id FK
        date period_start
        date period_end
        numeric amount
    }
    DEBT {
        uuid id PK
        uuid user_id FK
        string counterparty_name
        string direction
        numeric original_amount
    }
    DEBT_PAYMENT {
        uuid id PK
        uuid debt_id FK
        numeric amount
        uuid transaction_id FK
    }
    RECURRING_RULE {
        uuid id PK
        uuid user_id FK
        uuid account_id FK
        string frequency
        date next_date
    }
    RECURRING_OCCURRENCE {
        uuid id PK
        uuid rule_id FK
        date due_date
        string status
        uuid transaction_id FK
    }
    AUDIT_EVENT {
        uuid id PK
        uuid actor_user_id FK
        string entity_type
        uuid entity_id
        string action
    }
```

## Lectura de balances de grupo (no es tabla)

Para el par (A, B) en un grupo:

```text
owed_B_to_A =
    sum(split_B) for expenses posted where payer = A
  - sum(split_A) for expenses posted where payer = B
  + sum(settlements posted from B to A)
  - sum(settlements posted from A to B)
```

`owed_A_to_B = - owed_B_to_A`.
