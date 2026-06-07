# Architecture Decision Record 0002: Real-time Feature Storage Mechanism

## Status
Accepted

## Context
Aggregating 30 days of raw user clickstream logs on every home-screen execution is computationally impossible within a 120 ms latency boundary. Features must be aggregated asynchronously and served via an operational datastore supporting thousands of parallel lookups.

## Decision
We implement an **In-Memory Redis Cluster as our Online Feature Store Layer**.

## Consequences
* **Positive:** Delivers sub-4ms feature lookup execution for user historical profile vectors at 800+ RPS.
* **Negative:** High memory footprints across the cluster scale linearly with active B2C user counts. To mitigate financial burn, we enforce a strict 30-day Time-To-Live (TTL) eviction policy on all user feature keys.