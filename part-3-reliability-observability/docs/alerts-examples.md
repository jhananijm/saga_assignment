# Alert Examples (Production)

This document lists realistic alerts that should be configured for Saga’s multi-tenant AI platform.

---

## 1. Availability Alerts

### API 5xx error rate spike
- Condition: API 5xx > 2% for 5 minutes
- Severity: Critical
- Action: Page on-call

### Front Door backend health probe failing
- Condition: backend unhealthy > 1 minute
- Severity: Critical
- Action: Page on-call

---

## 2. Performance Alerts

### API latency degradation
- Condition: API p95 latency > 1500ms for 10 minutes
- Severity: High
- Action: Page on-call

### Inference latency degradation
- Condition: inference p95 latency > 4000ms for 10 minutes
- Severity: High
- Action: Page on-call

---

## 3. AKS Reliability Alerts

### Pod crash loop
- Condition: pod restart count > 5 within 10 minutes
- Severity: High
- Action: Page on-call

### Node saturation
- Condition: node CPU > 85% for 15 minutes
- Severity: Medium
- Action: Notify platform team

---

## 4. Async Pipeline Alerts

### Service Bus queue backlog
- Condition: active messages > 50,000 for 15 minutes
- Severity: High
- Action: Page on-call

### Dead letter queue increase
- Condition: DLQ message count > 100 in 5 minutes
- Severity: High
- Action: Page on-call

---

## 5. Database and Storage Alerts

### SQL connection failures
- Condition: connection errors > threshold for 5 minutes
- Severity: High
- Action: Page on-call

### Storage throttling
- Condition: throttling errors detected
- Severity: Medium
- Action: Notify platform team

---

## 6. AI Dependency Alerts

### Azure OpenAI throttling detected
- Condition: HTTP 429 rate > 5% for 10 minutes
- Severity: High
- Action: Page on-call

### OpenAI latency spike
- Condition: p95 OpenAI call latency > 8 seconds for 10 minutes
- Severity: Medium
- Action: Notify on-call

### Vector DB health check failing
- Condition: vector DB endpoint health check fails for 2 minutes
- Severity: High
- Action: Page on-call

---

## 7. Security Alerts

### Unexpected outbound traffic
- Condition: outbound traffic spike above baseline
- Severity: High
- Action: Notify security + platform

### WAF block spike
- Condition: WAF blocked requests > threshold
- Severity: Medium
- Action: Notify security

---

## 8. Tenant Isolation / Noisy Neighbor Alerts

### Tenant request spike
- Condition: one tenant consumes > 40% of total traffic for 10 minutes
- Severity: Medium
- Action: Notify platform team

### Tenant queue backlog
- Condition: tenant-specific queue depth > threshold
- Severity: Medium
- Action: Notify platform team

---

## Summary

The alerting strategy prioritizes customer impact and dependency failures while avoiding noise. Alerts should be tuned using SLOs and baseline patterns.
