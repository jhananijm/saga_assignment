# Architecture Overview

## 1. Overview

This Multi-tenant AI SaaS platform provides a shared cloud platform where multiple tenants can securely use AI-powered capabilities such as:

- AI chat and assistants
- Retrieval Augmented Generation (RAG)
- Document ingestion and embeddings generation
- Semantic and hybrid search
- Background AI workflows

The architecture is designed for production workloads and includes isolation boundaries, private networking, async processing, and resilient degradation strategies.

---

## 2. Architecture Diagram

![Azure Multi-Tenant AI Architecture](../../multi_tenant_architecture.png)

---

## 3. Platform Layers

### 3.1 Global Layer (Internet-facing entry point)

The platform entry is secured and routed through:

- **Azure AD B2C** for tenant user authentication
- **Azure Front Door** for global entry point and routing
- **WAF** for protection against OWASP threats
- **API Management (APIM)** to enforce tenant-aware policies

---

### 3.2 Web/API Layer

The application layer consists of:

- **Web App** hosted in Azure App Services
- **API** hosted in Azure App Services

The API acts as the tenant-aware gateway that resolves tenant identity, applies RBAC/ABAC authorization, and forwards requests to inference and async pipelines.

---

### 3.3 Containerized Compute Layer

The AI execution layer runs on:

- **AKS (Azure Kubernetes Service)**

Workloads inside AKS:

- **Inference Service (sync)** for low latency online inference
- **AI Workers (async)** for ingestion, embeddings, indexing, and batch jobs
- Optional background pipelines for ETL, evaluation, and fine-tuning workloads

---

### 3.4 Async Processing Layer

AI workloads such as embeddings generation and indexing are asynchronous and handled through:

- **Azure Service Bus** for durable job queueing
- **DLQ** for poison message isolation and safe replay
- **Event Grid** for job completion and workflow event notifications

---

### 3.5 Data Layer (Tenant-Isolated)

Each tenant has dedicated storage and database resources:

- **SQL Database or Cosmos DB**
- **Blob Storage / ADLS**
- **Redis Cache**

This ensures strong tenant isolation and reduces the blast radius of failures.

---

### 3.6 AI Services Layer

The AI service layer consists of:

- **Azure OpenAI** for LLM inference and embeddings generation
- **Azure AI Search** for indexing and retrieval
- **External vector database** such as Weaviate or Astra for semantic retrieval

---

### 3.7 Security & Observability

Security is enforced using:

- **Azure Key Vault**
- **Managed Identities**
- **Private Link / Private Endpoints**
- **Outbound allowlisting via NAT/Egress control**

Observability is implemented through:

- **Application Insights**
- **Log Analytics**
- Optional Sentinel integration for SIEM

---

## 4. Environment Separation

Separate environments are provisioned:

- **Dev**
- **Test**
- **Prod**

Each environment runs in separate subscriptions and VNets to ensure isolation and prevent accidental cross-environment impact.

---

## 5. Summary

This architecture provides a scalable and secure multi-tenant AI platform capable of supporting both real-time inference and long-running asynchronous AI pipelines, while meeting enterprise networking and compliance expectations.
