# Capacity Plan & Mathematical Infrastructure Sizing

### Operational Input Constants
* **Peak Traffic Throughput:** $800\text{ RPS}$
* **Target Engine System Allocation Limits:** Maximum $80\%$ Utilization Target across Core Pools
* **Single Engine Thread Execution Speed:** $45\text{ ms}$ processing duration per payload ($22.2\text{ requests/sec}$ per worker thread)

### Cluster Sizing Calculations

$$\text{Required Active Worker Threads} = \frac{800\text{ RPS}}{22.2\text{ Req/Sec}} = 36.03\text{ Active Threads}$$

Applying our targeted cluster head-room ceiling buffer ($80\%$ limit):

$$\text{Total System Target Worker Threads} = \frac{36.03}{0.80} = 45.04\text{ Threads}$$

Using 4 concurrent worker processes on our target hardware instances (`g5.xlarge` with 4 vCPUs):

$$\text{Target Production Cluster Replica Size} = \frac{45.04}{4} \approx 12\text{ Active Pod Instances}$$

### Infrastructure Cost Aggregations

* **Compute Tier:** 12 Instances of AWS `g5.xlarge` ($\$1.212\text{ / hour}$) $\approx \$10,470\text{ / month}$ (Unreserved Base Runtime Estimation). 
* *Optimization Strategy Applied:* Implementing strict Horizontal Pod Autoscaling (HPA) targeting dynamic bounds from $4$ up to $14$ nodes scales average monthly operational computing expenditures back down to **\$2,600 / Month**.