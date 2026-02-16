# Tenant Isolation Strategy

## 1. Isolation Goals

The platform must ensure:

- No tenant can access another tenant’s data
- One tenant cannot degrade platform performance for others
- Regulated tenants can be isolated further when required
- Tenant-specific scaling and quotas can be enforced

---

## 2. Tenant Isolation Model

### Tenant identity boundary
Tenant identity is derived from Azure AD B2C authentication and resolved by the API.

Each request must include:
- tenantId
- tenant plan/tier
- tenant permissions (RBAC/ABAC claims)

---

## 3. Data Isolation

Each tenant receives isolated resources:

- Dedicated **SQL/Cosmos**
- Dedicated **Blob/ADLS**
- Dedicated **Redis**

This provides strong data boundaries and supports compliance requirements.

---

## 4. Compute Isolation

### Shared AKS cluster
Default tenants run in a shared AKS cluster to optimize cost.

Isolation is enforced using:

- Kubernetes namespaces per tenant
- NetworkPolicies (deny-by-default)
- Resource quotas and limits per namespace
- Dedicated node pools for premium tenants if needed

---

## 5. Blast Radius Control

Blast radius is reduced through:

- APIM throttling per tenant
- Worker concurrency limits per tenant
- Per-tenant cache to avoid eviction conflicts
- Service Bus queue/topic separation

---

## 6. Isolation Tiers (recommended extension)

### Tier 1: Shared platform (default)
Shared AKS, isolated data per tenant.

### Tier 2: Dedicated data + encryption keys
Separate CMK and isolated databases for regulated tenants.

### Tier 3: Dedicated subscription and AKS
Full isolation for high compliance tenants.

---

## Summary

This model balances cost efficiency with strong tenant isolation and provides an upgrade path for stricter compliance tenants.
