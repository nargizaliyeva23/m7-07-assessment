# Incident Response Playbook: Emergency Model Rollback

### Trigger Conditions for Execution
Execute this manual immediately if any of the following parameters are violated:
1. **Critical SLO Exhaustion:** `HighAvailabilityBurnRate1Hour` fires via Slack/PagerDuty.
2. **Latency Bounds Exceeded:** $p_{95}$ latency on inference metrics slips past $\gt 120\text{ ms}$ for over 5 consecutive minutes.
3. **Invalid Routing Deployments:** Version mismatch flags detect divergent weights inside clusters (`ModelVersionRuntimeMismatchDetected` is true).

---

## Direct Resolution Action Sequence

### Step 1: Isolate and Verify Current Active Deployment State
Establish safe operational connections to the EKS serving node pools and read deployment version keys:
```bash
kubectl get deployments -n ml-serving -o wide