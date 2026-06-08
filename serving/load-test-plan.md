# Load Testing Plan (k6 Configuration)

800 RPS yük altında sistemin dayanıqlılığını və SLO limitlərini yoxlamaq üçün pilləli (ramp-up) load-test strategiyası tətbiq olunur.

```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '2m', target: 200 },  // 2 dəqiqə ərzində normal yükə qalxmaq
    { duration: '5m', target: 800 },  // Peak yükə (800 RPS) çatmaq və 5 dəqiqə test etmək
    { duration: '1m', target: 0 },    // Sürətli eniş və recovery yoxlanışı
  ],
  thresholds: {
    'http_req_duration': ['p(95)<120'], // p95 120ms-dən kiçik olmalıdır
    'http_req_failed': ['rate<0.001'],   // Səhv dərəcəsi 0.1%-dən az olmalıdır
  },
};

export default function () {
  const url = '[https://api.retail.internal/v1/recommend/sync](https://api.retail.internal/v1/recommend/sync)';
  const payload = JSON.stringify({
    user_id: "usr_998124",
    device_type: "ios"
  });
  const params = {
    headers: {
      'Content-Type': 'application/json',
      'X-Model-Version': 'v2.1.0'
    },
  };
  const res = http.post(url, payload, params);
  check(res, { 'status is 200': (r) => r.status === 200 });
  sleep(0.1);
}