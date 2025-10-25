flowchart TD
    A[Landing Page] --> B[Search Tours]
    B --> C{Tours Found?}
    C -->|Yes| D[Show Results]
    C -->|No| E[Show No Results Message]
    D --> F[Select Tour]
    F --> G[Tour Details]
    G --> H{User Logged In?}
    H -->|No| I[Redirect to Login]
    H -->|Yes| J[Proceed to Booking]
    I --> K[Login Register]
    K --> H
    J --> L[Enter Booking Details]
    L --> M[Payment Processing]
    M --> N{Payment Success?}
    N -->|Yes| O[Show Confirmation]
    N -->|No| P[Show Payment Error]
    O --> Q[Send Booking Confirmation Email]