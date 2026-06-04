# Architecture Overview

This document outlines the high-level architecture of the system, showing how different components interact with each other.

## System Architecture Diagram

```mermaid
graph TB
    subgraph Client["Client Layer"]
        WEB["🌐 Web Browser"]
        MOBILE["📱 Mobile App"]
    end
    
    subgraph CDN["Content Delivery"]
        CDN_SERVICE["CDN<br/>Static Assets"]
    end
    
    subgraph LoadBalancer["Load Balancing"]
        LB["Load Balancer<br/>API Gateway"]
    end
    
    subgraph Backend["Backend Services"]
        AUTH["🔐 Auth Service"]
        API["📡 REST API"]
        PROCESS["⚙️ Processing Service"]
    end
    
    subgraph Cache["Caching Layer"]
        REDIS["Redis Cache"]
    end
    
    subgraph Database["Data Layer"]
        POSTGRES["PostgreSQL<br/>Primary DB"]
        REPLICA["PostgreSQL<br/>Read Replica"]
    end
    
    subgraph Queue["Message Queue"]
        KAFKA["Kafka/RabbitMQ<br/>Event Bus"]
    end
    
    subgraph Workers["Background Workers"]
        WORKER1["Worker 1"]
        WORKER2["Worker 2"]
    end
    
    subgraph Observability["Observability"]
        LOGS["📊 Logging<br/>ELK Stack"]
        METRICS["📈 Metrics<br/>Prometheus"]
        TRACE["🔍 Tracing<br/>Jaeger"]
    end
    
    subgraph External["External Services"]
        EMAIL["📧 Email Service"]
        STORAGE["☁️ Object Storage<br/>S3/GCS"]
    end
    
    %% Client connections
    WEB --> CDN_SERVICE
    MOBILE --> CDN_SERVICE
    WEB --> LB
    MOBILE --> LB
    
    %% Load Balancer to Backend
    LB --> AUTH
    LB --> API
    
    %% Backend services
    AUTH --> REDIS
    AUTH --> POSTGRES
    API --> REDIS
    API --> POSTGRES
    API --> PROCESS
    
    %% Database replication
    POSTGRES --> REPLICA
    
    %% Processing and Queue
    PROCESS --> KAFKA
    KAFKA --> WORKER1
    KAFKA --> WORKER2
    
    %% Workers to Database
    WORKER1 --> POSTGRES
    WORKER2 --> POSTGRES
    
    %% External services
    WORKER1 --> EMAIL
    PROCESS --> STORAGE
    
    %% Observability
    AUTH -.-> LOGS
    API -.-> LOGS
    PROCESS -.-> LOGS
    WORKER1 -.-> LOGS
    WORKER2 -.-> LOGS
    
    AUTH -.-> METRICS
    API -.-> METRICS
    
    AUTH -.-> TRACE
    API -.-> TRACE
    
    style Client fill:#e1f5ff
    style Backend fill:#f3e5f5
    style Database fill:#fff3e0
    style Cache fill:#f1f8e9
    style Queue fill:#ede7f6
    style Workers fill:#fce4ec
    style Observability fill:#e0f2f1
    style External fill:#fff8e1
    style CDN fill:#f0f4c3
    style LoadBalancer fill:#e8eaf6
