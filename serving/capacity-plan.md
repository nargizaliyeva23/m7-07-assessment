# Capacity Plan and Resource Allocation Math

## Input Operational Specifications
* **Peak RPS Target:** 800 Requests Per Second
* **Total Allowed System Latency:** 120 ms
* **Allocated Container Inference Target (SLO Limit):** 80 ms

## Compute Instance Profiles (AWS ECS Fargate Instance Target)
Each container replica runs a Python Uvicorn engine configured with **4 parallel worker processes**. 
* **Tested Single-Worker Throughput:** ~25 RPS at an average internal latency of 45ms.
* **Total Capacity Per Replica Container:** $25 \text{ RPS} \times 4 \text{ workers} = 100 \text{ RPS}$.

## Required Production Fleet Scaling Calculation
To handle the absolute maximum peak load of 800 RPS securely without performance drops:
$$\text{Required Minimum Replicas} = \frac{\text{Peak RPS}}{\text{Capacity Per Replica}} = \frac{800}{100} = 8 \text{ Replicas}$$

We will provision a deployment configuration with a minimum baseline instance count of **8 replicas**, with autoscaling triggers configured to expand up to **12 replicas** if system-wide CPU utilization crosses 70%.