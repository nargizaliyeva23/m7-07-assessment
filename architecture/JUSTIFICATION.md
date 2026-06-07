```markdown
# Architectural Pattern Justification

For Scenario X, the design implements an **Online Synchronous Inference Pattern backed by a Dual-Layer Feature Store Engine**. 

### Selected Structural Topologies

1. **Decoupled Low-Latency Inference Path:**
   To satisfy the strict 120 ms end-to-end $p_{95}$ budget, features cannot be generated at runtime. The system stores user browsing history vectors (last 30 days) in an ultra-low latency **Redis Enterprise Online Feature Store** cluster ($\le 4\text{ ms}$ read overhead). The inference server acts as a thin orchestrator: fetches features, formats tensors, scores, and returns payloads.

2. **Pre-Calculated Static Fallbacks for Cold-Starts:**
   When a user context does not exist in the online feature store, fetching triggers an internal structural branch directly to a localized memory map containing popular or trending fallback items. This guarantees that cold-start paths bypass tensor math entirely, keeping the system resilient and bounded below 15 ms.

3. **In-Memory Multi-Variant A/B Layer:**
   A/B testing routing logic is native to the API runtime. The application routes traffic dynamically using weights embedded in the request context header (`X-Variant-Route`). This prevents network serialization overhead caused by upstream routing proxies or sidecars.