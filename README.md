# Argus

A self-hosted Kubernetes resource mapper and CI/CD visualizer.
Built as a DevOps lab project on a local k3s cluster.

## What this is

Two views over a real Kubernetes cluster:

1. **Resource map** — interactive graph of all cluster resources (namespaces, deployments, pods, services, ingresses)
2. **CI/CD timeline** — for each running deployment: which commit it came from, when it was deployed, link to the build

The two views are linked: click a deployment in the map, see its deploy history. Click a deploy, jump to the running pods.

## Tech stack

| Layer | Tool |
|---|---|
| Cluster | k3s (lightweight Kubernetes) |
| Frontend | Next.js + React Flow |
| Backend | .NET 8 Web API |
| CI | GitHub Actions |
| Registry | GHCR |
| Ingress | ingress-nginx (Helm) — planned |
| GitOps | ArgoCD (Helm) — planned |
| Observability | kube-prometheus-stack — planned |

## Status

🚧 Work in progress — hello-world apps deployed to k3s via GHCR.

- [x] Monorepo structure (`apps/`, `k8s/`, `.github/`)
- [x] .NET 8 backend with `/` and `/health` endpoints
- [x] Next.js frontend calling the backend
- [x] Multi-stage Dockerfiles for both apps
- [x] GitHub Actions building and pushing images to GHCR on every push to `main`
- [x] K8s manifests (Deployment + Service) for both apps running in k3s
- [ ] Ingress + local DNS (`app.local`)
- [ ] ArgoCD for GitOps deploys
- [ ] Actual resource mapper and CI/CD viewer

## Why

Two reasons:

- Hands-on with raw Kubernetes after working with managed platforms. Managed K8s is great, but understanding what it abstracts away matters.
- The "what commit is running where" question is one most teams answer with browser tab juggling. A unified view felt like a fun problem to build.

## How it works (Day 1)

```
push to main
  → GitHub Actions builds Docker images
  → images pushed to GHCR (tagged with :latest and :<commit-sha>)
  → k3s pulls the image on pod restart
  → frontend pod calls backend via K8s service discovery (http://backend:5000)
  → kubectl port-forward exposes frontend to browser (for now)
```

## Local setup

### Prerequisites

- WSL2 (Ubuntu 24.04)
- Docker
- k3s
- kubectl
- Node.js 24 (via nvm)
- .NET 8 SDK

### Run locally

```bash
# Backend
cd apps/backend
dotnet run

# Frontend
cd apps/frontend
npm run dev
```

### Deploy to k3s

```bash
kubectl apply -f k8s/manifests/backend.yaml
kubectl apply -f k8s/manifests/frontend.yaml

# Access frontend
kubectl port-forward svc/frontend 3000:3000
```

After pushing new images, trigger a rollout:

```bash
kubectl rollout restart deployment/backend
kubectl rollout restart deployment/frontend
```
