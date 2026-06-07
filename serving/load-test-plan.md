# Production Scale Validation Load Test Script (Locust)

```python
from locust import HttpUser, task, between

class RecommendationAPIStressTester(HttpUser):
    wait_time = between(0.1, 0.5)

    @task(10)
    def test_synchronous_recommendation_path(self):
        headers = {
            "Content-Type": "application/json",
            "X-Variant-Route": "canary-release-v2"
        }
        payload = {
            "user_id": "usr_test_bench_load",
            "client_timestamp": "2026-06-07T16:00:00Z"
        }
        self.client.post("/v1/recommendations/sync", json=payload, headers=headers)