# Kubernetes GitOps Infrastructure

A production-ready Kubernetes GitOps repository managed with Flux CD, featuring automated deployments, encrypted secrets management, and comprehensive infrastructure as code.

## 🛠️ Tech Stack

[![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)](https://kubernetes.io/)
[![Flux CD](https://img.shields.io/badge/Flux_CD-5468FF?style=for-the-badge&logo=flux&logoColor=white)](https://fluxcd.io/)
[![Talos Linux](https://img.shields.io/badge/Talos_Linux-FF7300?style=for-the-badge&logo=linux&logoColor=white)](https://www.talos.dev/)
[![Cilium](https://img.shields.io/badge/Cilium-F8C517?style=for-the-badge&logo=cilium&logoColor=black)](https://cilium.io/)
[![Traefik](https://img.shields.io/badge/Traefik_Proxy-24A1C1?style=for-the-badge&logo=traefikproxy&logoColor=white)](https://traefik.io/)
[![Longhorn](https://img.shields.io/badge/Longhorn-FF6F00?style=for-the-badge&logo=rancher&logoColor=white)](https://longhorn.io/)
[![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)](https://prometheus.io/)
[![Grafana](https://img.shields.io/badge/grafana-%23F46800.svg?style=for-the-badge&logo=grafana&logoColor=white)](https://grafana.com/)
[![Let's Encrypt](https://img.shields.io/badge/Let's_Encrypt-003A70?style=for-the-badge&logo=letsencrypt&logoColor=white)](https://letsencrypt.org/)
[![SOPS](https://img.shields.io/badge/SOPS-000000?style=for-the-badge&logo=mozilla&logoColor=white)](https://github.com/getsops/sops)
[![Vault](https://img.shields.io/badge/vault-FFEC6E?style=for-the-badge&logo=vault&logoColor=black)](https://www.vaultproject.io/)
[![Renovate](https://img.shields.io/badge/Renovate-1A1F6C?style=for-the-badge&logo=renovatebot&logoColor=white)](https://www.mend.io/renovate/)

## 🚀 Quick Start

### Prerequisites
- **kubectl** (v1.28+)
- **flux** (v2.0+)
- **sops** (v3.8+) with **age** (v1.1+)
- SSH access to this repository
- Age private key for secrets decryption

### Repository Overview

This repository manages:
- **2 environments**: Production and Staging
- **11 infrastructure components**: Cilium, Traefik, Longhorn, Prometheus, Vault, etc.
- **15 applications**: Media server stack (Plex, Radarr, Sonarr) + utilities
- **Automated dependency updates** via Renovate
- **SOPS-encrypted secrets** with Age encryption

## 📁 Repository Structure

```
k8s-infra/
├── clusters/          # Flux CD cluster configurations
│   ├── prod/         # Production cluster (15 apps)
│   └── staging/      # Staging cluster (2 apps)
├── infra/            # Infrastructure layer
│   ├── controllers/  # Helm releases (Cilium, Traefik, Longhorn, etc.)
│   └── configs/      # Configuration resources (secrets, ingresses)
├── apps/             # Application layer
│   ├── base/        # Base application manifests
│   ├── prod/        # Production overlays
│   └── staging/     # Staging overlays
└── assets/          # Platform-specific assets (Talos OS configs)
```

## 🏗️ Architecture

### Technology Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **OS** | Talos Linux | Immutable Kubernetes OS |
| **CNI** | Cilium | eBPF networking with L2 announcements |
| **Ingress** | Traefik | HTTP/HTTPS routing with TLS |
| **Storage** | Longhorn | Distributed block storage |
| **Certificates** | Cert-Manager | Automatic Let's Encrypt TLS |
| **Secrets** | SOPS + Vault | Encrypted secrets management |
| **Monitoring** | Prometheus Stack | Metrics, alerts, and dashboards |
| **GitOps** | Flux CD | Continuous deployment from Git |

### Deployment Flow

```
GitHub → Flux CD → 1. Infra Controllers → 2. Infra Configs → 3. Applications
```

Flux monitors the repository every minute and automatically applies changes in dependency order.

## 🔐 Secrets Management

Secrets are encrypted with **SOPS** using **Age** encryption:

```bash
# Encrypt a secret
sops --encrypt --in-place apps/base/my-app/secret.yaml

# Edit an encrypted secret
sops apps/base/my-app/secret.yaml

# Decrypt for viewing
sops --decrypt apps/base/my-app/secret.yaml
```

**Age public key**: `age1f9ndju9c2tp8vae2lkwrenx8sk4f0zk236wlzufq8hg302gkqatsalpapg`
**Private key location**: `~/.config/sops/age/keys.txt` (local), `flux-system/sops-age` (cluster)

## 🚢 Deployed Applications

### Production (15 apps)
- **Homepage** - Unified dashboard (`homepage.cruznet.io`)
- **Plex** - Media streaming with GPU acceleration (`plex.cruznet.io`)
- **Radarr** - Movie management (`radarr.cruznet.io`)
- **Sonarr** - TV series management (`sonarr.cruznet.io`)
- **Prowlarr** - Indexer manager (`prowlarr.cruznet.io`)
- **Sabnzbd** - Usenet downloader (`sabnzbd.cruznet.io`)
- **Seerr** - Media requests (`seerr.cruznet.io`)
- **Tautulli** - Plex monitoring (`tautulli.cruznet.io`)
- **Calibre** - Ebook management (`calibre.cruznet.io`)
- **Audiobookshelf** - Audiobook server (`audiobookshelf.cruznet.io`)
- **Linkding** - Bookmark manager (`linkding.cruznet.io`)
- **IT-Tools** - IT utilities (`it-tools.cruznet.io`)
- **Unifi** - Network controller (`unifi.cruznet.io`)
- **Uptime Kuma** - Uptime monitoring (`uptime-kuma.cruznet.io`)
- **Rustdesk** - Remote desktop (`rustdesk.cruznet.io`)

### Staging (2 apps)
- Homepage (`homepage.staging.cruznet.io`)
- Linkding (`linkding.staging.cruznet.io`)

## 🔧 Common Operations

### Adding a New Application

1. **Create base manifests**:
```bash
mkdir -p apps/base/my-app
cd apps/base/my-app
# Create: namespace.yaml, deployment.yaml, service.yaml,
#         ingress.yaml, certificate.yaml, kustomization.yaml
```

2. **Create production overlay**:
```bash
mkdir -p apps/prod/my-app
# Create kustomization.yaml referencing base
```

3. **Add to root overlay**:
```bash
# Edit apps/prod/kustomization.yaml
resources:
  - my-app
```

4. **Commit and push**:
```bash
git add apps/
git commit -m "Add my-app application"
git push
# Flux will deploy automatically within 1 minute
```

### Updating an Application

```bash
# Edit the deployment
vim apps/base/my-app/deployment.yaml

# Update the image
image: myregistry/my-app:v1.1.0@sha256:newdigest...

# Commit and push
git commit -am "Update my-app to v1.1.0"
git push
```

### Manual Flux Reconciliation

```bash
# Reconcile Git source
flux reconcile source git flux-system

# Reconcile infrastructure
flux reconcile kustomization infra-controllers --with-source
flux reconcile kustomization infra-configs --with-source

# Reconcile applications
flux reconcile kustomization apps --with-source
```

### Check Deployment Status

```bash
# View all Kustomizations
flux get kustomizations

# View all HelmReleases
flux get helmreleases -A

# Check application status
kubectl get all -n my-app

# View Flux logs
flux logs --level=error
```

## 📊 Monitoring and Observability

- **Grafana**: `https://grafana.cruznet.io` (Dashboards and visualization)
- **Longhorn UI**: `https://longhorn.cruznet.io` (Storage management)
- **Hubble UI**: `https://hubble.cruznet.io` (Network observability)
- **Prometheus**: Available via Grafana (Metrics storage)

## 🤖 Automated Updates

**Renovate** automatically creates PRs for dependency updates:

- **Automerge anytime** (patch updates): calibre, cert-manager, it-tools, prometheus-stack, radarr, sonarr, etc.
- **Manual review required**: cilium, longhorn, traefik, vault, and all major version updates
- **Schedule**: Automerge between 1-4 AM Pacific time

## 🛠️ Troubleshooting

### Application Not Starting

```bash
kubectl get pods -n my-app
kubectl describe pod <pod-name> -n my-app
kubectl logs <pod-name> -n my-app
```

### Certificate Issues

```bash
kubectl get certificate -n my-app
kubectl describe certificate my-app-cert -n my-app
kubectl logs -n cert-manager deploy/cert-manager
```

### Flux Not Syncing

```bash
flux get sources git
flux get kustomizations
kubectl logs -n flux-system deploy/kustomize-controller
```

### Ingress Unreachable

```bash
kubectl get svc traefik -n traefik  # Check LoadBalancer IP
kubectl get ingressroute -n my-app
kubectl logs -n traefik -l app.kubernetes.io/name=traefik
```

## 🔥 Emergency Procedures

**Suspend Flux reconciliation**:
```bash
flux suspend kustomization infra-controllers
flux suspend kustomization infra-configs
flux suspend kustomization apps
```

**Resume reconciliation**:
```bash
flux resume kustomization infra-controllers
flux resume kustomization infra-configs
flux resume kustomization apps
```

**Rollback via Git**:
```bash
git revert <commit-hash>
git push
# Flux will automatically apply the revert
```

## 📖 Documentation

For comprehensive documentation covering:
- Complete architecture details
- Infrastructure component configurations
- Networking and storage setup
- Security best practices
- Detailed troubleshooting guides
- Disaster recovery procedures

See the full documentation in this README (sections above) or refer to:
- [Flux CD Documentation](https://fluxcd.io/docs/)
- [Kustomize Documentation](https://kustomize.io/)
- [SOPS Documentation](https://github.com/getsops/sops)

## 🤝 Contributing

1. Create a feature branch
2. Test changes in staging first
3. Update documentation if needed
4. Create a pull request
5. Wait for CI checks and Renovate validation

## 📝 Best Practices

- ✅ Always encrypt secrets with SOPS
- ✅ Use digest pinning for container images
- ✅ Test in staging before production
- ✅ Define resource limits for all workloads
- ✅ Monitor Flux reconciliation status
- ✅ Review Renovate PRs regularly
- ✅ Keep Age private key backed up securely

## 📊 Repository Stats

- **Total YAML files**: 288
- **Infrastructure components**: 11
- **Production applications**: 15
- **Staging applications**: 2
- **Managed namespaces**: 21+
- **TLS certificates**: 14+
- **Persistent volumes**: 23+

## 🔗 Quick Links

- **GitHub Repository**: https://github.com/dapacruz/k8s-infra
- **Flux System**: `kubectl get all -n flux-system`
- **Renovate Dashboard**: Check GitHub Issues for "Renovate Dashboard"

## 📧 Maintainer

**@Dapacruz**

---

**Last Updated**: 2026-03-25
**Kubernetes Version**: v1.28+
**Flux Version**: v2.0+
