flowchart TD
    subgraph Roles["👥 Roles"]
        AD(["👑 Admin"])
        OW(["🏨 Hotel Owner"])
        EM(["👨‍🍳 Employee"])
        ST(["🎓 Student"])
    end

    subgraph FE["🖥️ Next.js PWA"]
        FA["/(admin)/*"]
        FB["/(hotel)/*"]
        FC["/(student)/*"]
    end

    subgraph API["⚙️ NestJS API"]
        G1[AuthGuard]
        G2[RolesGuard]
        G3[AvailabilityGuard]
        M1[Auth Module]
        M2[Hotels Module]
        M3[Menu Module]
        M4[Orders Module]
        M5[Analytics Module]
        M6[Employees Module]
        M7[Notifications Module]
        M8[Admin Module]
        M9[Audit Module]
        M10[Disputes Module]
    end

    subgraph DB["🔥 Firestore"]
        D1[(users)]
        D2[(hotels)]
        D3[(menus)]
        D4[(orders)]
        D5[(bills)]
        D6[(employees)]
        D7[(colleges)]
        D8[(notifications)]
        D9[(auditLogs)]
        D10[(reviews)]
        D11[(disputes)]
    end

    subgraph Infra["🚀 Infrastructure"]
        I1[Firebase Auth]
        I2[Firebase Storage]
        I3[FCM]
        I4[Bull Queue + Redis]
        I5[Vercel]
        I6[Railway/Render]
    end

    AD --> FA
    OW --> FB
    EM --> FB
    ST --> FC

    FA --> G1 --> G2 --> M8
    FB --> G1 --> G2 --> M2
    FB --> G1 --> G2 --> M3
    FB --> G1 --> G2 --> M4
    FB --> G1 --> G2 --> M5
    FB --> G1 --> G2 --> M6
    FC --> G1 --> G2 --> G3 --> M4
    FC --> G1 --> G2 --> M2
    FC --> G1 --> G2 --> M3

    M1 --> D1
    M2 --> D2
    M3 --> D3
    M4 --> D4
    M4 --> D5
    M5 --> D4
    M6 --> D6
    M7 --> D8
    M7 --> I3
    M8 --> D1
    M8 --> D2
    M8 --> D7
    M9 --> D9
    M10 --> D11

    API --> I4
    I4 --> I3

    FE --> I1
    FE --> I2
    FE -.->|onSnapshot direct| D2
    FE -.->|onSnapshot direct| D3
    FE -.->|onSnapshot direct| D4
    FE -.->|onSnapshot direct| D8

    FE --> I5
    API --> I6

    style Roles fill:#0d0d1a,stroke:#818CF8,color:#818CF8
    style FE fill:#0d1a2d,stroke:#38BDF8,color:#38BDF8
    style API fill:#1a0d2d,stroke:#818CF8,color:#818CF8
    style DB fill:#2d1a0d,stroke:#F59E0B,color:#F59E0B
    style Infra fill:#0d2d1a,stroke:#10B981,color:#10B981