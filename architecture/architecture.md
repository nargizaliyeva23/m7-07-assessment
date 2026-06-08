graph TD
    User([Mobile App User]) -->|HTTPS Request 800 RPS| AGW[API Gateway / AWS ALB]
    AGW -->|Route based on A/B Header| SVC[Inference Service: FastAPI Pods]
    
    subgraph Model Serving Cluster (ECS Fargate)
        SVC -->|1. Fetch User History < 15ms| REDIS[(Redis Online Feature Store)]
        SVC -->|2. Local Model Inference < 50ms| MEM[Inference Engine: Baked-in Model v1.2.0]
    end

    subgraph Data & MLOps Control Plane
        DB[(Snowflake Data Warehouse)] -->|Offline Training Data| TRN[Feature Store: Feast Offline]
        TRN -->|Daily Sync| REDIS
        REG[(MLflow Model Registry)] -->|Promote via CI/CD| SVC
    end

    SVC -->|Export Metrics| PROM[Prometheus / Grafana Monitoring]