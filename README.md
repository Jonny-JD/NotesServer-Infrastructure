# NotesServer Infrastructure

Kubernetes infrastructure for [NotesServer](https://cybernotes.space) — a web application for creating and managing public and private notes.

Includes Helm charts, GitHub Actions CI/CD pipelines, and Ingress configuration with TLS via Let's Encrypt.

---

## Stack

- **Kubernetes**: K3s (self-hosted on bare metal)
- **Package manager**: Helm
- **CI/CD**: GitHub Actions
- **Ingress**: Traefik + Let's Encrypt (cert-manager)
- **Environments**: `prod` (https://cybernotes.space) and `feature` (separate namespace)

---

## Repository Structure

```
.
├── .github/
│   ├── CICD-notesserver-feature.yaml      # CI/CD pipeline for feature environment
│   ├── CICD-notesserver-prod.yaml         # CI/CD pipeline for prod environment
│   ├── deploy-ingress-feature.yml         # Ingress deployment for feature
│   ├── deploy-ingress-prod.yml            # Ingress deployment for prod
│   ├── deploy-letsencrypt-feature.yml     # TLS setup for feature
│   └── deploy-letsencrypt-prod.yml        # TLS setup for prod
│
└── k8s/helm/
    ├── backend/                            # Backend Helm chart
    │   ├── templates/
    │   │   ├── deployment-backend.yaml
    │   │   └── service-backend.yaml
    │   ├── Chart.yaml
    │   ├── values.yaml
    │   ├── values_feature.yaml
    │   └── values_prod.yaml
    │
    ├── frontend/                           # Frontend Helm chart
    │   ├── templates/
    │   │   ├── deployment-frontend.yaml
    │   │   └── service-frontend.yaml
    │   ├── Chart.yaml
    │   ├── values.yaml
    │   ├── values_feature.yaml
    │   └── values_prod.yaml
    │
    ├── ingress/                            # Ingress + middleware
    │   ├── templates/
    │   │   ├── ingress.yaml
    │   │   └── middleware.yaml
    │   ├── Chart.yaml
    │   ├── values.yaml
    │   ├── values_feature.yaml
    │   └── values_prod.yaml
    │
    └── cluster-issuer/                     # Let's Encrypt ClusterIssuer
        ├── templates/
        │   └── cluster-issuer.yaml
        ├── Chart.yaml
        ├── values.yaml
        ├── values_feature.yaml
        └── values_prod.yaml
```

---

## Environments

| Environment | Namespace   | URL                          |
|-------------|-------------|------------------------------|
| Production  | `prod`      | https://cybernotes.space     |
| Feature     | `feature`   | separate namespace in cluster |

Each chart has a dedicated `values_prod.yaml` and `values_feature.yaml` for environment-specific configuration.

---

## CI/CD

Pipelines are defined in `.github/` and triggered automatically on push to the corresponding branches.

Each pipeline covers the full cycle:
1. Build Docker images
2. Run tests
3. Push images to container registry
4. Deploy to the K3s cluster via Helm

---

## Related Repositories

- [NotesServer Backend](https://github.com/Jonny-JD/NotesServer-B-) — Java 21, Spring Boot, PostgreSQL
- [NotesServer Frontend](https://github.com/Jonny-JD/NotesServer-F-) - React.js, TS, Vite
