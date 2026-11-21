# dell-cluster

My homelab Kubernetes cluster: K3s on a Dell Optiplex, fully managed from Git with Flux.
Started in September 2026 during a hands-on Kubernetes course, and kept running since.

Nothing is applied by hand: every change goes through a commit, Flux reconciles it,
and Renovate opens PRs for image and chart updates.

## What runs on it

| Layer | Components |
| :-- | :-- |
| Cluster | K3s, single node (`v1.36.3+k3s1`), Ubuntu 26.04 |
| GitOps | Flux (Kustomize controller + Helm controller) |
| Secrets | SOPS + Age, decrypted by Flux at apply time |
| Ingress & TLS | Traefik (K3s built-in), cert-manager with Cloudflare DNS-01 |
| Public access | Cloudflare Tunnel (`cloudflared`), outbound-only, no open port |
| Monitoring | kube-prometheus-stack (Prometheus, Grafana, Alertmanager) |
| Updates | Renovate, self-hosted as a Kubernetes CronJob |
| Apps | linkding, audiobookshelf, homepage |

## Repository layout

```
clusters/staging/        # Flux entry point: one Kustomization per layer
infrastructure/
  controllers/           # cert-manager, Renovate
  configs/               # ClusterIssuers (depends on controllers)
monitoring/              # kube-prometheus-stack HelmRelease
apps/
  base/                  # plain manifests per app
  staging/               # overlay: ingress, tunnels, secrets, patches
.devcontainer/ + mise    # pinned tooling: kubectl, flux, sops, helm, k9s...
```

## Choices

- **Flux**: pull-based, lightweight on a single small node.
- **SOPS + Age**: secrets stay encrypted in Git, no extra controller to run.
  Only `data`/`stringData` are encrypted, so manifests stay readable in diffs.
- **Kustomize for my apps, Helm for third-party stacks**: my manifests stay plain YAML;
  cert-manager and kube-prometheus-stack come from their charts.
- **Dev Container + mise**: same tool versions on any machine.

## Things I learned the hard way

- `volumes` sits next to `containers`, `volumeMounts` inside the container.
- A ConfigMap mount is read-only: homepage needed an `emptyDir` for its logs.
- To put a ConfigMap and a Secret in the same directory, use a `projected` volume.
- I tested Flux on purpose: deleted a Service from Git to check pruning, pushed
  a broken image tag to check that the rolling update keeps the old pods running.
- Moved from a manually created TLS Secret to cert-manager (DNS-01 through Cloudflare).

## Not done yet

- Single node: no HA, no node failure testing.
- No backups of persistent volumes yet.
- Two images still use `:latest` (`cloudflared` for linkding, `renovate`),
  so Renovate cannot track them.
