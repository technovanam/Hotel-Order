flowchart TD
    A([Any Firestore read or write request]) --> B{Is it via Firebase Admin SDK — NestJS Backend?}
    B -->|Yes — Admin SDK| C[Bypasses all security rules — full access]
    B -->|No — Client SDK from browser| D[Security rules evaluated]

    D --> E{Which collection?}

    E --> F[hotels / menus]
    F --> G{Request type?}
    G -->|Read| H[Allow — any authenticated user]
    G -->|Write| I[Deny — must go through NestJS API]

    E --> J[orders]
    J --> K{Request type?}
    K -->|Read| L{uid == order.studentId OR uid == hotel owner/employee?}
    L -->|Yes| M[Allow read]
    L -->|No| N[Deny]
    K -->|Write — Create| O{uid == request.studentId?}
    O -->|Yes| P[Allow — student creates own order]
    O -->|No| Q[Deny]
    K -->|Write — Update status| R[Deny — status updates only via NestJS API]

    E --> S[users]
    S --> T{uid == document id?}
    T -->|Yes| U[Allow read + write own document]
    T -->|No| V[Deny]

    E --> W[auditLogs / bills / analytics]
    W --> X[Deny ALL client writes — backend only via Admin SDK]

    E --> Y[notifications]
    Y --> Z{uid == notification.userId?}
    Z -->|Yes| AA[Allow read + mark as read]
    Z -->|No| AB[Deny]

    style C fill:#0f2d1f,color:#10B981,stroke:#10B981
    style I fill:#4B1515,color:#ff6b6b,stroke:#ff6b6b
    style N fill:#4B1515,color:#ff6b6b,stroke:#ff6b6b
    style Q fill:#4B1515,color:#ff6b6b,stroke:#ff6b6b
    style R fill:#4B1515,color:#ff6b6b,stroke:#ff6b6b
    style V fill:#4B1515,color:#ff6b6b,stroke:#ff6b6b
    style X fill:#4B1515,color:#ff6b6b,stroke:#ff6b6b
    style AB fill:#4B1515,color:#ff6b6b,stroke:#ff6b6b