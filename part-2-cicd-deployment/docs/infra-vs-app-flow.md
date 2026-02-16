# Infrastructure vs Application Change Flow

The platform separates infrastructure delivery from application delivery to reduce risk.

---

## 1. Infrastructure Change Flow (IaC Pipeline)

Triggered by:
- changes under `infra/`
- manual dispatch

Stages:
1. Terraform/Bicep validate
2. Plan generated on PR
3. Apply executed only on merge with approvals (test/prod)

Responsibilities:
- VNets and subnets
- private endpoints
- Key Vault setup
- AKS provisioning and scaling configuration
- ACR provisioning
- Service Bus/Event Grid configuration
- App Service Plans and slots

---

## 2. Application Change Flow (App Pipeline)

Triggered by:
- changes under `app/` or `services/`

Stages:
1. lint + unit tests
2. container build
3. push image to ACR
4. deploy to dev automatically
5. promote to test and prod via release gates

Responsibilities:
- API/Web deployments
- inference service updates
- async worker updates
- helm chart upgrades

---

## 3. Why separation is important

Benefits:
- infra changes are reviewed more strictly
- app deployments remain fast
- reduced chance of environment outages due to infra mistakes
- easier auditing and rollback

---

## Summary
Infrastructure and application changes are delivered through separate workflows with different approval gates and risk levels.
