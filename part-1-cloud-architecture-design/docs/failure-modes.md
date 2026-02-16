# Failure Modes and Degradation Strategies

This document describes common failure modes in a multi-tenant AI platform and how the architecture handles them.

---

## 1. Failure Mode: Vector DB unavailable

### Impact
RAG retrieval fails and responses may degrade.

### Mitigation
- Retry with exponential backoff
- Fallback to Azure AI Search keyword/hybrid retrieval
- Return partial response with trace ID if retrieval is unavailable

---

## 2. Failure Mode: Azure OpenAI throttling

### Impact
Inference latency increases or requests fail.

### Mitigation
- Circuit breaker + backoff retries
- Cache frequently used prompts and retrieval contexts
- Async job submission mode for long-running requests

---

## 3. Failure Mode: Queue backlog (traffic spike)

### Impact
Async processing delays and worker saturation.

### Mitigation
- Service Bus buffers workload spikes
- AKS worker autoscaling based on queue length
- Tenant-level concurrency caps to prevent noisy neighbor effects

---

## 4. Failure Mode: Poison messages

### Impact
Workers repeatedly fail processing the same message.

### Mitigation
- DLQ enabled on Service Bus
- Messages routed to DLQ after max retries
- Replay and debugging supported without blocking pipelines

---

## 5. Failure Mode: Tenant noisy neighbor

### Impact
One tenant consumes excess compute or request throughput.

### Mitigation
- APIM quotas and throttling
- Per-tenant Redis cache
- Kubernetes namespace resource quotas
- Dedicated node pools for premium tenants

---

## 6. Failure Mode: Storage/DB transient failures

### Impact
Temporary request failures and slow pipelines.

### Mitigation
- Retry logic in API and workers
- Graceful fallback to cached data
- Store-and-forward pattern through async queueing

---

## 7. Degradation Strategy Summary

The platform remains usable even during partial outages by supporting:

- Async job submission when real-time inference is not possible
- Cache-first retrieval for repeated requests
- Keyword-only fallback when vector DB is unavailable
- DLQ isolation for non-recoverable failures
