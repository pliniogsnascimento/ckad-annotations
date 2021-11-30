# Labels, Selectors and Annotations

Labels and selectors are a standard method to group things together.
```bash
kubectl run nginx --image=nginx -l="app=nginx,tier=frontend"
```
Annotations are for informational purpose.

# Rolling Updates and Rollback Deployments
When creating deployments, a `rollout` is triggered. Each `rollout` creates a `revision`. When container image are updated, a new rollout is triggered. Commands:
```bash
kubectl rollout history deployment/my-deployment
kubectl rollout status deployment/my-deployment

# Restart
kubectl rollout restart deployment/my-deployment

# Rollback
kubectl rollout undo deployment/my-deployment
```
