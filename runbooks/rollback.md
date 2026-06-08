# Emergency Incident Rollback Playbook

> **Scope:** Immediate automated rollback for the Scenario X Personalization Service cluster.

## Part 1: Automated Rollback Trigger Criteria
Execute this manual runbook immediately if any of the following automated monitoring alarms are firing in Grafana:
1. **`HighInferenceLatencyServiceBreach`** status is active for > 2 continuous minutes (Inference Container latency > 80ms).
2. **`ModelVersionMismatchDetected`** flag returns true (Active bad tags or invalid environment state).
3. System HTTP 5XX failure responses cross **1%** within a rolling 5-minute telemetry window.

## Part 2: Step-by-Step Rollback Execution Steps

### Step 1: Identify the Last Known Stable Image Tag
Locate the previous stable model tag from the CI/CD deployment history. For this cycle, the last verified stable configuration state is **`v1.1.9`**.

### Step 2: Force AWS ECS Cluster Task Rollback
Run the following script command within your authenticated terminal shell terminal to safely point traffic away from the broken deployment:

```bash
# Force the ECS service to update and roll back to the stable image version definition
aws ecs update-service \
  --cluster retail-prod-ml-cluster \
  --service recommendation-inference-service \
  --task-definition recommendation-service-v1-1-9 \
  --force-new-deployment