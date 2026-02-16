# Secrets Handling in CI/CD

A secure CI/CD pipeline must avoid long-lived secrets and enforce least privilege.

---

## 1. Authentication in GitHub Actions

### Recommended: GitHub OIDC Federation
GitHub Actions authenticates to Azure using OpenID Connect.

This eliminates the need to store:
- client secrets
- service principal passwords

Authentication flow:
GitHub Runner → OIDC token → Azure federated credential → short-lived access token

---

## 2. Environment-Scoped Access

Each environment has a dedicated Azure identity:
- gha-dev-deployer
- gha-test-deployer
- gha-prod-deployer

Each identity has RBAC scoped to only its subscription/resource group.

---

## 3. Runtime Secrets

Application secrets are stored in:
- Azure Key Vault

Access is provided through:
- Managed Identity for App Services
- Workload Identity / Managed Identity for AKS pods

Examples of secrets:
- OpenAI endpoint keys (if needed)
- vector DB tokens
- database connection strings
- encryption keys

---

## 4. Key Vault Integration

App Services:
- Key Vault references in app settings

AKS:
- Key Vault CSI driver or External Secrets Operator

---

## Summary
This secrets strategy provides:
- no long-lived secrets in GitHub
- centralized auditing and rotation in Key Vault
- least privilege per environment
