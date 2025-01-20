# release-fluxcd-demo

This repository demonstrates GitOps using FluxCD with multiple clusters (staging/production) and different deployment methods:
- Kustomize deployment with [podinfo](https://github.com/stefanprodan/podinfo)
- Helm deployment with [Redis](https://github.com/bitnami/charts/tree/main/bitnami/redis)
- OCI deployment with [Grafana](https://grafana.com/docs/grafana/latest/setup-grafana/installation/kubernetes/)

## Repository Structure

```
├── apps
│   ├── base
│   │   ├── grafana
│   │   ├── podinfo
│   │   └── redis
│   ├── production
│   └── staging
├── infrastructure
│   ├── configs
│   └── controllers
└── clusters
    ├── production
    └── staging
```

## Prerequisites

1. A Kubernetes cluster v1.28 or newer
2. GitHub account and [personal access token](https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line)
3. Flux CLI installed:
   ```sh
   brew install fluxcd/tap/flux
   # or
   curl -s https://fluxcd.io/install.sh | sudo bash
   ```

## Applications

### 1. Podinfo (Kustomize)
- Simple web application
- Deployed using Kustomize
- Different configurations for staging/production

### 2. Redis (Helm)
- In-memory data store
- Deployed using Helm
- Configured with different persistence settings per environment

### 3. Grafana (OCI)
- Monitoring dashboard
- Deployed using OCI artifacts
- Different datasource configurations per environment

## Bootstrap Clusters

### Staging

```sh
export GITHUB_TOKEN=<your-token>
export GITHUB_USER=<your-username>
export GITHUB_REPO=release-fluxcd-demo

# Bootstrap staging
flux bootstrap github \
    --context=staging \
    --owner=${GITHUB_USER} \
    --repository=${GITHUB_REPO} \
    --branch=main \
    --personal \
    --path=clusters/staging
```

### Production

```sh
# Bootstrap production
flux bootstrap github \
    --context=production \
    --owner=${GITHUB_USER} \
    --repository=${GITHUB_REPO} \
    --branch=main \
    --personal \
    --path=clusters/production
```

## Directory Structure Details

### Apps Base Configuration
- `apps/base/podinfo`: Kustomize manifests for podinfo
- `apps/base/redis`: Helm release configuration for Redis
- `apps/base/grafana`: OCI image configuration for Grafana

### Environment-Specific Configurations
- `apps/staging`: Staging-specific configurations
- `apps/production`: Production-specific configurations

### Infrastructure
- `infrastructure/controllers`: Common controllers (ingress-nginx, cert-manager)
- `infrastructure/configs`: Shared configurations

## Monitoring Deployments

Watch the deployments:
```sh
# Get all Flux resources
flux get all

# Watch Helm releases
flux get helmreleases --all-namespaces --watch

# Watch kustomizations
flux get kustomizations --watch
```
