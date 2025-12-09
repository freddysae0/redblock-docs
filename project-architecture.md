```mermaid
flowchart LR
    %% === CLIENTS ===
    subgraph Clients
        P[Player]
        C[Coach]
        A[Admin]
    end

    subgraph Frontends
        GC[Game Client (Unity)]
        WA[Web App (React/Next)]
    end

    P --> GC
    P --> WA
    C --> WA
    A --> WA

    %% === PUBLIC EDGE ===
    subgraph Public_Edge ["Public Edge / Internet-facing"]
        APIGW[API Gateway / Edge]
        MATCHGW[Match Gateway (WebSocket)]
    end

    GC -->|HTTPS / REST| APIGW
    WA -->|HTTPS / REST| APIGW
    GC -->|WebSocket| MATCHGW

    %% === INTERNAL SERVICES ===
    subgraph Internal_Services ["Internal Services (Private Network)"]
        AUTH[Auth Service]
        CORE[Core API Service]
        UGC[UGC / Scenario Service]
        MATCH[Match / Game Session Service]
        ANALYTICS[Analytics Service]
    end

    APIGW --> AUTH
    APIGW --> CORE
    APIGW --> UGC

    MATCHGW --> MATCH

    %% === DATA STORES & EVENTS ===
    subgraph Data_Stores ["Data Stores & Messaging"]
        MAINDB[(Main DB<br/>Users, Sessions, Scenarios)]
        ANALYTICSDB[(Analytics DB<br/>Aggregated Stats)]
        STORAGE[(Object Storage<br/>Scenario Assets)]
        BROKER[(Message Broker)]
    end

    CORE --> MAINDB
    UGC --> MAINDB
    MATCH --> MAINDB

    UGC --> STORAGE

    %% Events
    MATCH -->|SessionCompleted events| BROKER
    CORE -->|Domain events| BROKER
    BROKER --> ANALYTICS
    ANALYTICS --> ANALYTICSDB

    %% === EXTERNAL INTEGRATIONS ===
    subgraph External_Integrations ["External Integrations"]
        STEAM[Steam OAuth]
        STRIPE[Stripe / Payments]
        EMAIL[Email Provider]
        DISCORD[Discord Webhooks]
    end

    AUTH --> STEAM
    CORE --> STRIPE
    CORE --> EMAIL
    ANALYTICS --> DISCORD

```