# Incident Scenario and Response Flow

## Incident Scenario
### Title: Azure OpenAI throttling causes platform-wide inference degradation

---

## 1. What happened
During peak usage hours, multiple tenants generate high traffic to the AI inference API.
Azure OpenAI starts returning **HTTP 429 (rate limit exceeded)** responses.

This results in:
- increased inference latency
- retries causing higher compute usage
- some requests failing
- backlog building in async pipelines

---

## 2. Detection (alerts triggered)

The following alerts trigger:
- OpenAI 429 rate > 5% for 10 minutes
- Inference p95 latency > 4000ms
- API 5xx error rate > 2%
- Service Bus queue depth increasing (workers retrying)

---

## 3. Incident Response Flow (On-call process)

### Step 1: Triage (0-10 minutes)
- On-call acknowledges alert
- Confirms impact by checking dashboards:
  - APIM/API error rates
  - Inference latency
  - OpenAI dependency failures
- Confirms this is a dependency issue (429 spike)

### Step 2: Containment (10-20 minutes)
Immediate mitigation actions:
- Enable circuit breaker mode in inference service (stop aggressive retries)
- Reduce concurrency of inference pods (or apply rate limiting)
- Apply APIM throttling policies per tenant (prevent noisy neighbor)
- Degrade gracefully:
  - switch to cached responses for repeated queries
  - disable RAG temporarily if vector calls also slow down

### Step 3: Communication (20-30 minutes)
- Post incident summary in Teams/Slack incident channel
- Notify stakeholders with:
  - affected services
  - mitigation steps applied
  - ETA for next update

### Step 4: Recovery (30-60 minutes)
- Monitor OpenAI 429 rates
- Scale AKS pods back gradually once throttling reduces
- Drain queue backlog by increasing worker replicas
- Validate health checks and confirm latency returns to normal

### Step 5: Post-incident closure
- Close incident after 30 minutes stable metrics
- Document timeline and root cause
- Create follow-up tasks

---

## 4. Root Cause
The platform exceeded Azure OpenAI throughput quota due to high concurrent tenant usage.
Retry logic amplified load.

---

## 5. Preventive Actions (Postmortem Improvements)

### Technical improvements
- Implement adaptive throttling per tenant
- Enforce token usage quotas and request budgets
- Add tenant-specific rate limits in APIM
- Introduce OpenAI fallback model selection (if multiple deployments exist)
- Improve caching of repeated prompts and embeddings
- Implement async mode for heavy prompts

### Operational improvements
- Add proactive quota monitoring dashboards
- Set alerts on predicted quota exhaustion
- Run load tests before peak release windows

---

## Summary
This incident demonstrates how dependency throttling can degrade a multi-tenant AI platform and how the system should respond using graceful degradation, rate limiting, and autoscaling.
