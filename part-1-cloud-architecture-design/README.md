# Multi-Tenant AI SaaS Platform on Azure

This repository contains a production-grade reference architecture for Multi-Tenant AI SaaS Platform.

The design focuses on secure tenant isolation, scalable AI workloads, containerized services, private networking, and resilience patterns required for enterprise-grade AI platforms.

---

## Architecture Diagram

![Azure Multi-Tenant AI Architecture](/../diagrams/multi_tenant_architecture.png)

---

## Design Goals

This architecture addresses the following requirements:

- **Multi-tenant model** (tenant isolation, data boundaries, blast radius control)
- **Web/API layer + async AI workloads**
- **Containerized services** and integration with external **vector databases**
- **Secrets and identity management** using Key Vault and Managed Identity
- **Networking** using VNets, Private Endpoints, Private Link, outbound egress control
- **Environment separation** (Dev / Test / Prod)
- **Failure handling and degradation strategies**

---

## High-Level Components

### Global Layer
- Azure AD B2C
- Front Door
- WAF
- API Management

### Production (Core Runtime)
- App Services (Web App + API)
- AKS Cluster (Inference + Workers)
- Service Bus + DLQ
- Event Grid

### Tenant-Isolated Data Plane
- Tenant-specific SQL/Cosmos
- Tenant-specific Blob/ADLS
- Tenant-specific Redis

### AI Layer
- Azure OpenAI
- Azure AI Search
- External Vector Database (Weaviate / Astra)

### Security and Monitoring
- Key Vault
- Managed Identity
- Private Link / Private Endpoints
- Log Analytics + Application Insights

---

## Documentation

- [Architecture Overview](docs/architecture.md)
- [Major Design Rationale](docs/rationale.md)
- [Tenant Isolation Strategy](docs/tenant-isolation.md)
- [Failure Modes & Degradation](docs/failure-modes.md)

---

