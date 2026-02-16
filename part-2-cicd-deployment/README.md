# Part 2 - GitHub Actions CI/CD Deployment Strategy

This folder contains the GitHub Actions based deployment strategy for multi-tenant AI SaaS platform on Azure.

The strategy covers:
- Environment separation (Dev / Test / Prod)
- Deployment approach (zero downtime, rollback)
- Secrets handling in CI/CD
- Infrastructure vs application change flow

---

## Folder Structure

- `docs/` contains the written strategy and rationale.
- `workflows/` contains example GitHub Actions workflow templates.

---

## Key Design Principles

- **Build once, deploy many**: artifacts are promoted from Dev → Test → Prod.
- **Environment isolation** using GitHub Environments and Azure subscriptions.
- **No long-lived credentials**: GitHub OIDC authentication is used.
- **Zero downtime deployments** using App Service slots and AKS progressive rollout.
- **Fast rollback** using slot swap rollback and Helm rollback.

---

## Contents

### Documentation
- [CI/CD Strategy](docs/cicd-strategy.md)
- [Deployment Approach](docs/deployment-approach.md)
- [Rollback Strategy](docs/rollback-strategy.md)
- [Secrets Handling](docs/secrets-handling.md)
- [Infra vs App Change Flow](docs/infra-vs-app-flow.md)

### Workflow Templates
- `workflows/ci.yml`
- `workflows/deploy.yml`
- `workflows/infra.yml`

> Note: Workflows are stored as templates and not under `.github/workflows/` to avoid execution.
