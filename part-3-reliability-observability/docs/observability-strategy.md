# Observability Strategy (Metrics, Logs, Tracing, Alerting)

This document describes a production-grade observability and monitoring strategy for Saga’s multi-tenant AI platform on Azure.

The platform includes:
- Front Door + WAF + APIM
- Web/API services hosted on App Services
- AKS workloads (Inference + Workers)
- Service Bus + Event Grid for async orchestration
- Tenant-isolated data (SQL/Cosmos, Storage, Redis)
- Azure OpenAI + AI Search + external vector DB (Weaviate/Astra)

---

## 1. Observability Goals

The monitoring strategy must support:
- fast detection of customer-impacting incidents
- tenant-level visibility (noisy neighbor detection)
- end-to-end request tracing for debugging
- operational readiness for AI workloads (latency, token usage, throttling)
- clear SLO-driven alerting instead of noisy alerts

---

## 2. Logging Strategy

### Central log store
All logs are centralized in:
- **Azure Log Analytics Workspace**

Sources:
- App Service application logs
- AKS container logs (stdout/stderr)
- Kubernetes events
- Service Bus logs (message throughput + dead-lettering)
- Azure SQL diagnostic logs
- Front Door + WAF logs
- APIM request logs

### Log standards
All application logs should be structured JSON and include:
- `tenantId`
- `correlationId` (trace id)
- `requestId`
- `serviceName`
- `environment`
- `userId` (if applicable)
- `operationName`
- `latencyMs`

This enables tenant-level filtering and faster root cause analysis.

---

## 3. Metrics Strategy

### Core platform metrics (Golden Signals)
Monitor golden signals across all layers:
- Latency
- Traffic
- Errors
- Saturation

Key metric sources:
- App Services metrics (HTTP response time, 5xx rate)
- AKS metrics (CPU/memory, pod restarts, node saturation)
- Service Bus metrics (active messages, dead-letter count)
- SQL metrics (DTU/CPU usage, connection failures)
- Redis metrics (cache hit ratio, memory usage)
- OpenAI metrics (rate limiting, token usage)
- AI Search metrics (query latency, indexer failures)

---

## 4. Distributed Tracing

### Tracing implementation
Use **Application Insights** for distributed tracing.

Tracing must cover:
- API request → inference service
- inference → Azure OpenAI
- inference → AI Search
- workers → Service Bus consumption
- workers → vector DB updates
- storage indexing pipelines

Each request must propagate correlation headers:
- `traceparent`
- `x-correlation-id`

This enables end-to-end visibility for debugging.

---

## 5. Alerting Strategy

### Alert categories
Alerts should be grouped into:
- Availability alerts (customer impact)
- Performance alerts (latency degradation)
- Dependency alerts (OpenAI throttling, vector DB down)
- Capacity alerts (AKS saturation, queue backlog)
- Security alerts (unexpected egress, WAF spikes)

### Alert routing
Alert actions:
- Notify on-call engineer (PagerDuty / Teams)
- Create incident ticket (ServiceNow/Jira)
- Post summary to incident channel

Alerts should be environment-aware:
- Prod alerts are paging
- Dev/test alerts are informational

---

## 6. Dashboards

Recommended dashboards:
- Global traffic overview (Front Door, APIM)
- API health (RPS, p95 latency, error rate)
- AKS workload health (pods, CPU/mem, restart counts)
- Async pipeline (queue depth, DLQ count, worker throughput)
- AI dependencies (OpenAI latency, throttling, vector DB health)
- Tenant-level usage dashboard (top tenants, cost drivers)

---

## Summary

This observability strategy provides:
- full request visibility via tracing
- centralized logging for root cause analysis
- SLO-based alerting to reduce noise
- tenant-aware dashboards to detect noisy neighbor issues
- AI-specific monitoring for throttling and cost anomalies
