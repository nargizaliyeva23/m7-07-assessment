# Architecture Decision Record 0001: Model Asset Delivery Strategy

## Status
Accepted

## Context
The production recommendation model asset is a 420 MB optimized ONNX artifact. During scale-out events under a peak load of 800 RPS, Kubernetes pods must initialize rapidly to prevent cascading starvation across the active node cluster. We must choose between baking the model directly into the Docker image or mounting it dynamically at container runtime from an S3 bucket via a shared volume or initialization script.

## Decision
We choose to **Bake the Model Asset Directly into the Docker Container Image** during the CI/CD compilation phase.

## Consequences
* **Positive:** Pod initialization scales linearly and rapidly without third-party object store network dependencies during a scale-up event. Pods become ready to receive traffic within $\le 8\text{ seconds}$.
* **Positive:** Immutability guarantee: The image tag explicitly binds both the code version and the exact model weights version, eliminating runtime drift.
* **Negative:** The Docker image size expands to $\approx 680\text{ MB}$, extending image push/pull durations inside the CI pipeline to roughly 45 seconds.