# Deployment Approach (Zero Downtime)

This platform includes both App Services (Web/API) and AKS workloads (Inference/Workers). Deployment strategy differs per runtime.

---

## 1. Web/API Deployment (App Services)

### Recommended Approach: Deployment Slots
Deploy Web/API using App Service slots:
- `staging` slot
- `production` slot

Deployment steps:
1. Deploy new build to staging slot
2. Run smoke tests against staging
3. Swap staging into production

Benefits:
- near zero downtime
- rollback is instant via swap-back
- safer deployments with validation gates

---

## 2. AKS Deployment (Inference + Workers)

### Recommended Approach: Helm-based deployment
AKS workloads are deployed using Helm charts.

Deployment steps:
1. `helm upgrade --install` new version
2. readiness checks
3. progressive traffic shift (canary or blue-green)

---

## 3. Progressive Delivery (Recommended)

### Canary rollout (preferred)
Traffic gradually shifts:
- 10% → 25% → 50% → 100%

Automated rollback triggers:
- error rate increase
- latency increase
- failed readiness probes

---

## 4. Async Workloads Deployment
AI Workers and Background Jobs are deployed separately from inference services.

Benefits:
- background pipeline updates do not affect inference latency
- worker scaling can be tuned independently

---

## Summary
This deployment approach ensures:
- minimal downtime for API and inference workloads
- safe promotion of changes with validation gates
- controlled rollout for high-risk AI changes
