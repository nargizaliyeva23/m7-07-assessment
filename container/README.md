# Container Packaging Blueprint

### Structural Strategy: Baked Weights
Following the architectural decisions rendered in **ADR-0001**, the 420 MB model file (`recommender_v2.onnx`) is physically copied into the execution layer during image generation. 

### Size Footprint Accounting Matrix

| Sub-component Layer | Contributed Weight (MB) | Engineering Optimization Method |
| :--- | :--- | :--- |
| **Base OS Layer** | 120 MB | Utilizing strictly minimized `python:3.11-slim` image |
| **System Dependencies** | 40 MB | Only compiling C runtime engine packages (`libgomp1`) |
| **Python Libraries** | 100 MB | Stripping developer libraries; pinning `onnxruntime-gpu`, `fastapi` |
| **Model Weights Asset** | 420 MB | Frozen, pre-compiled ONNX layer graphs |
| **Total Target Size** | **~680 MB** | Highly performant footprint targeting ultra-fast scaling |

### Security Measures
1. **Rootless Execution Environment:** Container changes standard context execution privilege levels directly down to a non-privileged system user (`mlops_runner`).
2. **Explicit Layer Hardening:** Build parameters discard transient `Poetry` artifact caches, keeping the target filesystem clean.