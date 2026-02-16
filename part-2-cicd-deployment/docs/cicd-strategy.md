# CI/CD Strategy - GitHub Actions (Dev / Test / Prod)

This document describes a production-grade GitHub Actions CI/CD strategy for deploying a multi-tenant AI platform on Azure.

The platform consists of:
- Web/API services hosted on Azure App Services
- AI inference and async workloads running on AKS
- Messaging backbone using Service Bus and Event Grid
- External integrations like Azure OpenAI and external vector databases (Weaviate/Astra)
- Tenant-isolated data services (SQL/Cosmos, Storage, Redis)

---

## 1. Environment Separation

### GitHub Environments
GitHub Environments will be created as:
- `dev`
- `test`
- `prod`

Each environment holds:
- environment-scoped variables
- approval requirements
- deployment history and audit logs

### Protection Rules
Recommended setup:
- **dev**: no approval (fast iteration)
- **test**: approval optional, but require all CI checks
- **prod**: manual approval required and restricted deployers

### Azure Subscription Separation
Each environment is deployed into a separate Azure subscription:
- Dev subscription
- Test subscription
- Production subscription

This ensures the strongest blast radius separation.

---

## 2. Branch and Release Strategy

### Branch Mapping
- `feature/*`: pull request only, no deployments
- `main`: auto deploy to `dev`
- `release/v*` or GitHub Releases: deploy to `test` and then `prod`

### Release Versioning
Artifacts are tagged with:
- immutable git SHA tag (`sha-<commit>`)
- semantic version tag (`vX.Y.Z`)

This enables traceability and rollback.

---

## 3. Pipeline Structure

The CI/CD system is split into two independent workflows:

### A. Application Pipeline
Responsible for:
- unit tests
- linting
- container image build and push
- deployment to App Services and AKS

### B. Infrastructure Pipeline (IaC)
Responsible for:
- provisioning resources
- updating networking, VNets, private endpoints
- AKS cluster upgrades and scaling configuration
- Azure service configuration updates

---

## 4. Deployment Flow (Build once, deploy many)

The recommended strategy is:
1. Build artifact once
2. Push artifact to ACR / artifact store
3. Deploy the same artifact to dev
4. Promote the same artifact to test
5. Promote the same artifact to prod

This avoids "works in dev but not prod" drift caused by rebuilds.

---

## 5. Summary

This CI/CD strategy provides:
- strong environment isolation
- secure authentication via OIDC
- zero downtime deployments
- safe rollback patterns
- clean separation of infrastructure and application change workflows
