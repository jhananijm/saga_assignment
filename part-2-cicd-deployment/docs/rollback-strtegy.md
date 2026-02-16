# Rollback Strategy

Rollback must be fast, predictable, and environment-specific.

---

## 1. App Services Rollback

### Slot Swap Rollback
If deployment causes errors:
- swap production back to staging

This restores the previous stable version immediately.

---

## 2. AKS Rollback

### Helm rollback
AKS workloads use Helm, allowing rollback to the previous revision:

- `helm rollback <release> <revision>`

This restores the previous stable deployment without requiring a rebuild.

---

## 3. Rollback Triggers

Rollback can be triggered by:
- failed smoke tests
- failed readiness checks
- increased 5xx error rates
- latency regression
- downstream dependency failures (OpenAI throttling, vector DB issues)

---

## 4. Deployment Traceability

All deployments must be traceable by:
- git SHA tag
- semantic version tag
- GitHub Deployment History

---

## Summary
Rollback is achieved using:
- App Service slot swap-back
- Helm rollback in AKS
- immutable version tags for reliable restoration
