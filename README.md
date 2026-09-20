# Dell Optiplex Kubernetes Homelab

A GitOps-managed Kubernetes cluster running on Dell Optiplex hardware, built as part of a hands-on Kubernetes course.

## Architecture

- **Cluster:** Bare-metal Kubernetes on Dell Optiplex nodes
- **GitOps:** Flux CD with Kustomize overlays
- **Secret Management:** SOPS with Age encryption
- **Environment:** Staging overlay with promotion-ready structure

## Stack

| Component | Tool |
|---|---|
| GitOps Controller | Flux CD |
| Configuration | Kustomize |
| Secret Encryption | SOPS + Age |
| Cluster Admin | kubectl, kubectx, kubens, k9s |
| Package Management | Helm |
| Dev Environment | Dev Containers + Mise |

## Repository Structure

```
clusters/staging/       # Flux bootstrap and cluster-level config
apps/base/              # Base application manifests (Kustomize)
apps/staging/           # Staging overlay
scripts/                # Cluster setup automation
.devcontainer/          # Reproducible dev environment
```

## Key Decisions

- **Flux over ArgoCD:** Chosen for its Git-native pull model and lightweight footprint on constrained hardware.
- **SOPS + Age over Sealed Secrets:** Simpler key management, no controller dependency, encryption at rest in Git.
- **Dev Containers + Mise:** Ensures every contributor has identical tooling (kubectl, flux, sops, helm, k9s) regardless of host OS.
- **Kustomize over Helm for apps:** Keeps application manifests transparent and auditable without template abstraction.

## Status

Actively being built as part of a GitOps/Kubernetes learning course.

