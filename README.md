# Notes — Infrastructure

Kubernetes infrastructure for the **Notes** full-stack pet project — a web application for creating and managing public and private notes.

Contains Helm charts and GitHub Actions pipelines that handle deployment of all application components to a self-hosted bare metal cluster.

## Tech Stack

- **Kubernetes** — K3s (self-hosted, bare metal)
- **Package manager** — Helm
- **Ingress** — Traefik + Let's Encrypt (cert-manager)
- **CI/CD** — GitHub Actions (self-hosted runner)

## How It Works

This repository is the deployment layer in a GitOps-style setup. It does not build anything — it receives a signal from the application repositories and deploys the specified image to the cluster via Helm.

```
backend / frontend repos
        │
        │  repository_dispatch (image repo + commit SHA tag)
        ▼
GitHub Actions (self-hosted runner)
  ├── helm upgrade --install backend   → namespace prod / feature
  └── helm upgrade --install frontend  → namespace prod / feature
```

Ingress and TLS (Let's Encrypt) are deployed separately via `workflow_dispatch` and don't change on every push.

## Repository Structure

```
.
├── .github/workflows/
│   ├── CICD-notesserver-prod.yaml       # Deploy backend/frontend to prod
│   ├── CICD-notesserver-feature.yaml    # Deploy backend/frontend to feature
│   ├── deploy-ingress-prod.yaml         # Ingress deployment (prod)
│   ├── deploy-ingress-feature.yml       # Ingress deployment (feature)
│   ├── deploy-letsencrypt-prod.yml      # TLS / ClusterIssuer (prod)
│   └── deploy-letsencrypt-feature.yml   # TLS / ClusterIssuer (feature)
│
└── k8s/helm/
    ├── backend/                         # Backend Helm chart
    ├── frontend/                        # Frontend Helm chart
    ├── ingress/                         # Ingress + Traefik middleware
    └── cluster-issuer/                  # Let's Encrypt ClusterIssuer
```

Each Helm chart has `values_prod.yaml` and `values_feature.yaml` for environment-specific configuration. Image repository and tag are passed dynamically via `--set` on every deploy.

## Environments

| Environment | Namespace | URL                           |
|-------------|-----------|-------------------------------|
| Production  | `prod`    | https://cybernotes.space      |
| Feature     | `feature` | separate namespace in cluster |

## Related Repositories

- [NotesServer Backend](https://github.com/Jonny-JD/NotesServer-B-) — Java 21, Spring Boot, PostgreSQL
- [NotesServer Frontend](https://github.com/Jonny-JD/NotesServer-F-) — React, TypeScript, Vite

## About

This project was built for my portfolio. It demonstrates:
- Managing Kubernetes infrastructure with Helm charts
- GitOps-style deployment triggered by `repository_dispatch` events
- Multi-environment setup (prod / feature) from a single chart
- TLS certificate automation with Let's Encrypt and cert-manager
- Running GitHub Actions on a self-hosted runner in a bare metal cluster
