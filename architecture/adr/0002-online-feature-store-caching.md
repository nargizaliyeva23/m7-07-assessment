# ADR 0001: Selection of Redis as Online Feature Store for Low-Latency Features

## Status
Accepted

## Context
Scenario X requires historical features (30 days of user browsing and purchases) to compute recommendations dynamically on every home-screen load. With an end-to-end p95 latency budget of 120ms, reading raw historical logs directly from the primary relational database or data warehouse during the request cycle is impossible.

## Decision
We will use an in-memory Redis cluster paired with Feast as our online feature store. All user profile vectors and aggregated 30-day behavioral sequences will be pre-computed and synchronized daily/hourly into Redis.

## Consequences
* **Positive:** Network read latency drops to <15ms, giving the model service plenty of headroom to infer within its 80ms allocation.
* **Negative:** Increased infrastructure costs due to maintaining an in-memory database cluster.