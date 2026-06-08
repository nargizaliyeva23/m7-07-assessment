# Infrastructure Capacity Plan: Scenario X

## 1. Core Core Traffic Assumptions & Metrics
* **Peak Throughput:** 800 Requests Per Second (RPS)
* **Target p95 Latency Budget (End-to-End):** 120 ms
  * *Internal Model Inference Budget:* 40 ms
  * *Redis Feature Retrieval Budget:* 15 ms
  * *Network & Routing Overhead:* 65 ms
* **Model Footprint:** 450 MB (RAM-da uncompressed halda ~1.2 GB yer tutur).

## 2. Resource Utilization & Replica Math
Tutaq ki, 1 Pod (Replica) daxilində modelimiz single-thread ilə 1 sorğunu ortalama **40 ms** (0.04 saniyə) ərzində emal edir.

$$\text{Capacity per Pod} = \frac{1 \text{ saniyə}}{0.04 \text{ saniyə/sorğu}} = 25 \text{ RPS}$$

Təhlükəsizlik və resurs piklərini idarə etmək üçün CPU saturasiyasını maksimum 70% səviyyəsində saxlayırıq (Safety Factor = 0.7):

$$\text{Effective Capacity per Pod} = 25 \text{ RPS} \times 0.7 = 17.5 \text{ RPS}$$

800 RPS pik yükü qarşılamaq üçün lazım olan minimal Pod (Replica) sayı:

$$\text{Required Replicas} = \frac{800 \text{ RPS}}{17.5 \text{ RPS/Pod}} \approx 45.7 \rightarrow \textbf{46 Replicas}$$

## 3. Hardware Recommendation & Cost Estimate (AWS)
* **Compute Choice:** `c6i.xlarge` instances (4 vCPU, 8 GiB RAM).
* **Pod Allocation:** Hər bir Pod üçün `limits` olaraq `1 vCPU` və `2 GiB RAM` təyin edilir. Bir instance-da 3 Pod yerləşdirilir.
* **Total Nodes Needed:** $46 \text{ Pods} / 3 \approx 16 \text{ Nodes}$.
* **Monthly Cost Estimate:** 16 x `c6i.xlarge` ($0.17x/hour) $\approx \$1,980/\text{month}$.