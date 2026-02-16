# Brief Rationale for Major Design Decisions

This section provides short justification for each major Azure service chosen in the architecture.

---

## Edge and API Entry

### Azure Front Door
Chosen as the global entry point because it provides low-latency routing, edge caching, and supports global failover patterns for SaaS platforms.

### WAF
Used to block common web attacks (OWASP Top 10) before traffic reaches the backend services.

### API Management (APIM)
Acts as the tenant policy enforcement layer, enabling:
- tenant quotas and throttling
- JWT validation
- API versioning
- routing to Dev/Test/Prod environments

---

## Identity and Tenant Context

### Azure AD B2C
Designed for customer-facing identities and supports multi-tenant SaaS authentication patterns such as MFA and federated logins.

### Tenant Resolver (API responsibility)
Ensures every request is mapped to a tenantId and tenant plan. This enables consistent enforcement of data boundaries and rate limits.

---

## Compute Layer

### AKS
Selected because it supports:
- containerized microservices
- worker-based async processing
- optional GPU node pools
- better control for hosting vector databases such as Weaviate

### Inference Service (sync)
Provides predictable low latency online responses and isolates real-time inference from background processing.

### AI Workers (async)
Handles ingestion, embeddings, indexing, and batch jobs without blocking user requests. Can scale independently based on queue length.

---

## Async Backbone

### Service Bus
Chosen because it provides durable queueing with retry semantics and supports high reliability job processing.

### Dead Letter Queue (DLQ)
A production requirement to isolate poison messages and allow safe replay without blocking pipelines.

### Event Grid
Used for lightweight event notifications such as job completed or failed status updates.

---

## Data Layer and Tenant Boundaries

### Blob Storage / ADLS
Cost-efficient and scalable storage for documents, chunks, and AI artifacts. Easy to isolate per tenant.

### SQL Database / Cosmos DB
Provides strong tenant-level data boundaries. SQL is suitable for relational workloads while Cosmos is optimized for high-scale partitioned access.

### Redis Cache
Tenant-level caching reduces latency and prevents noisy neighbor cache eviction.

---

## Search and RAG

### Azure AI Search
Managed indexing and retrieval service with hybrid search capabilities. Also acts as a fallback when vector search is degraded.

### Weaviate / Astra (Vector DB)
Provides semantic similarity search for embeddings.
- Weaviate offers full control when self-hosted
- Astra reduces operational overhead as a managed service

---

## Security

### Key Vault
Centralized secrets and key management with audit trails and rotation.

### Managed Identity
Eliminates hardcoded secrets and supports secure passwordless service-to-service authentication.

### Private Link / Private Endpoints
Ensures sensitive services are reachable only via private IP addresses, reducing public exposure.

### NAT / Egress Allowlist
Controls outbound access to external model endpoints and external vector databases, supporting compliance and preventing data exfiltration.

---

## Observability

### Application Insights
Provides tracing and performance monitoring at request level for the Web/API and inference services.

### Log Analytics
Central logging and metrics platform for AKS and Azure services.

---

## Environment Separation

### Separate Subscriptions and VNets for Dev/Test/Prod
Provides the strongest isolation boundary and reduces risk of accidental impact across environments.

### APIM routing per environment
Ensures a controlled entry point with consistent enforcement of policies.
