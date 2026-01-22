# sky-watch-pro
Enterprise-grade weather application with MERN stack and Kubernetes orchestration
graph TD
    %% User Layer
    User[End User Device]
    Browser[React SPA (Browser)]

    %% Networking Layer
    LB[AWS Load Balancer / Ingress]
    CDN[CloudFront CDN]

    %% Application Layer
    subgraph "Backend Cluster (Kubernetes/Docker)"
        APIGateway[Node.js API Gateway]
        Worker[Background Worker Service]
    end

    %% Data Layer
    Redis[(Redis Cache)]
    Mongo[(MongoDB Primary)]

    %% External Services
    OWM[OpenWeatherMap API]
    Auth[JWT / Auth Middleware]

    %% Connections
    User -->|HTTPS| CDN
    CDN -->|Static Assets| Browser
    Browser -->|API Requests| LB
    LB --> APIGateway

    %% Backend Logic
    APIGateway -->|1. Check Cache| Redis
    Redis -- Miss --> APIGateway
    Redis -- Hit --> APIGateway
    APIGateway -- If Miss --> OWM
    APIGateway -->|2. Read/Write User Data| Mongo
    
    %% Async Logic
    APIGateway -->|Emit Event| Worker
    Worker -->|Send Email/Alert| User
