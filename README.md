# AI Task Platform Infrastructure Repository

This directory is structured as a standalone GitOps repository for Argo CD.

## Layout
- `base/`: shared Kubernetes manifests.
- `overlays/staging`: staging-specific image tags + ingress host.
- `overlays/production`: production-specific image tags + ingress host.
- `argocd/`: Argo CD `Application` manifests.

## Apply Secrets
`base/secret.example.yaml` is a template only. Create a real secret before syncing:

```bash
kubectl create namespace ai-task-platform
kubectl -n ai-task-platform create secret generic ai-task-secrets \
  --from-literal=JWT_SECRET='<strong-random-secret>'
```

## Argo CD Setup (k3s-friendly)
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl apply -f argocd/application-staging.yaml
kubectl apply -f argocd/application-production.yaml
```

## Manual Kustomize Deploy
```bash
kubectl apply -k overlays/staging
kubectl apply -k overlays/production
```

## CI/CD Integration
Application repository CI updates image tags in:
- `overlays/staging/kustomization.yaml`
- `overlays/production/kustomization.yaml`

Argo CD auto-sync then rolls out the new version.
