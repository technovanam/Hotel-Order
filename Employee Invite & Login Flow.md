sequenceDiagram
    autonumber
    actor O as 🏨 Hotel Owner
    actor E as 👨‍🍳 Employee
    participant FE as Next.js Frontend
    participant NE as NestJS API
    participant FS as Firestore
    participant FA as Firebase Auth
    participant BQ as Bull Queue
    participant EM as Email Service

    O->>FE: /(hotel)/employees → Add Employee
    O->>FE: Enters name, email, permission flags
    FE->>NE: POST /api/employees/invite (email, name, hotelId, permissions)
    NE->>FA: verifyIdToken → role: owner
    NE->>FS: Create employees/{id} (status: invited, permissions, hotelId)
    NE->>BQ: Push email job (invite link)
    BQ->>EM: Send invite email to employee
    EM-->>E: Email with invite link
    NE-->>FE: { status: invited }
    FE->>FE: Show employee in pending list

    E->>FE: Clicks invite link → /(hotel)/accept-invite?token=xxx
    FE->>NE: POST /api/employees/accept-invite (token)
    NE->>FS: Validate invite token → get hotel name + employee name
    NE-->>FE: { hotelName, employeeName }
    FE->>FE: Show password creation screen

    E->>FE: Sets password
    FE->>FA: createUserWithEmailAndPassword(email, password)
    FA-->>FE: ID Token

    FE->>NE: POST /api/auth/verify (ID Token)
    NE->>FA: verifyIdToken → UID
    NE->>FS: Create users/{uid} (role: employee)
    NE->>FS: Update employees/{id} (status: active, uid linked)
    NE-->>FE: { user, hotelId, permissions }
    FE->>FE: Store permissions in Zustand
    FE->>FE: Redirect → /(hotel)/dashboard (restricted employee view)