flowchart LR
    subgraph Browser["🌐 Browser / PWA"]
        A[User grants notification permission]
        B[Firebase SDK getToken]
        C[FCM Token stored in Zustand]
    end

    subgraph NestJS["⚙️ NestJS Backend"]
        D[Store fcmToken in Firestore users doc]
        E[Business event triggers — order confirmed, status changed, payment etc]
        F[Push job to Bull Queue]
    end

    subgraph Queue["📬 Bull Queue Worker"]
        G[Job picked up by worker]
        H[Fetch target user fcmToken from Firestore]
        I[Firebase Admin messaging send]
    end

    subgraph Firebase["🔥 Firebase"]
        J[FCM Servers]
        K[Firestore notifications collection]
    end

    subgraph Device["📱 Student / Owner Device"]
        L[Push notification arrives]
        M[In-app bell badge via onSnapshot]
    end

    A --> B --> C
    C -->|POST /api/users/profile| D
    E --> F --> G --> H --> I
    I --> J --> L
    NestJS -->|Write notification doc| K
    K -->|onSnapshot listener| M

    style Browser fill:#0d1a2d,stroke:#38BDF8,color:#38BDF8
    style NestJS fill:#1a0d2d,stroke:#818CF8,color:#818CF8
    style Queue fill:#1a1a0d,stroke:#F59E0B,color:#F59E0B
    style Firebase fill:#2d0d0d,stroke:#F43F5E,color:#F43F5E
    style Device fill:#0d2d1a,stroke:#10B981,color:#10B981