# MLOps Design Dossier: Scenario X — Personalized In-App Recommendations

## Executive Summary
This system delivers real-time, personalized product recommendations for a high-traffic mobile retail application. Leveraging the user's past 30 days of browsing and purchase behavior, the pipeline serves inferencing requests at a peak scale of 800 RPS while maintaining a strict p95 latency profile under 120ms. The architecture utilizes an online feature store (Redis) for millisecond-level feature retrieval, continuous A/B testing routing via an API Gateway, and automated multi-window burn-rate alerting to ensure maximum platform reliability.

## System Key Parameters
| Metric / Component | Specification |
| :--- | :--- |
| **Peak Throughput** | 800 Requests Per Second (RPS) |
| **End-to-End p95 Latency Budget** | 120 ms |
| **Model Serving Container p95 Target**| 80 ms (leaves 40ms for network & gateway overhead) |
| **Service Level Objective (SLO)** | 99.9% successful responses within < 80ms over 1-hour window |
| **Model Artifact Size** | 240 MB (LightGBM/XGBoost Classifier serialized via Joblib) |
| **Inference Hardware Choice** | AWS ECS Fargate (vCPU: 2, RAM: 4GB per replica) |
| **Calculated Production Scaling** | 8 active replicas minimum (handles up to 100 RPS per instance) |
| **Estimated Monthly Compute Cost**| $364.80 USD (Based on ECS & Redis cache layer cluster) |

## Repository Navigation
* [Architecture Design](./architecture/architecture.md) & [Trade-offs Justification](./architecture/JUSTIFICATION.md)
* [ML Lifecycles & Gates](./lifecycle/lifecycle.md)
* [Production Docker Engine Configuration](./container/Dockerfile)
* [API Contract (OpenAPI 3.1 Spec)](./api/openapi.yaml)
* [Capacity Planning & Load Testing](./serving/capacity-plan.md)
* [CI/CD GitOps Automation](./cicd/.github/workflows/deploy-model.yml)
* [Prometheus Alerting Protocols](./monitoring/alerts.yaml)
* [Emergency Rollback Runbook](./runbooks/rollback.md)

## Open Questions for the Core Engineering Team
1. **Feature Store Hydration Frequency:** Are upstream streaming pipelines (Kafka/Flink) updating the Redis online feature store instantly upon a purchase event, or is there a batch sync delay from the data warehouse?
2. **Cold-Start Fallback Definitions:** Is the business product team providing dynamically updated static lists (e.g., top trending by geo-location) for cold-start users, or do we pull this from a global fallback recommendation model?