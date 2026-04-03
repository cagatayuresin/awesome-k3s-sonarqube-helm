# Awesome K3s SonarQube Helm

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![Helm](https://img.shields.io/badge/Helm-v3-blue?logo=helm)](https://helm.sh)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-K3s-326CE5?logo=kubernetes)](https://k3s.io)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![SonarQube](https://img.shields.io/badge/SonarQube-26.3_CE-4E9BCD?logo=sonarqube)](https://www.sonarsource.com/products/sonarqube/)

A production-ready SonarQube Community Edition Helm chart optimized for K3s with automatic Let's Encrypt SSL support via cert-manager.

## Features

- **Automatic SSL** - Let's Encrypt certificates via cert-manager
- **Persistent Storage** - Data survives pod restarts using local-path
- **Fully Configurable** - Domain, secrets, JVM options, and image tags via values.yaml
- **Production Ready** - Resource limits, health checks, security contexts, and sysctl tuning
- **K3s Optimized** - Uses Traefik ingress and local-path storage class
- **Elasticsearch Ready** - Automatic `vm.max_map_count` configuration via init container
- **JVM Tuning** - Configurable heap sizes for web, CE, and search processes
- **Single Command Deploy** - Get SonarQube running in minutes

## Prerequisites

1. **K3s cluster** with Traefik ingress controller (included by default)
2. **cert-manager** installed for SSL certificates:

   ```bash
   kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.14.4/cert-manager.yaml
   ```

3. **Helm 3.x** installed
4. **DNS configured** - Your domain's A record pointing to your K3s node's public IP
5. **Minimum 4GB RAM** available on the node (SonarQube + Elasticsearch requirement)

## Quick Start

### 1. Clone the repository

```bash
git clone https://github.com/cagatayuresin/awesome-k3s-sonarqube-helm.git
cd awesome-k3s-sonarqube-helm
```

### 2. Create your custom values file

```bash
cp sonarqube-helm/values.yaml my-values.yaml
```

### 3. Edit your values

```yaml
# my-values.yaml
domain: sonar.yourdomain.com

letsencrypt:
  email: your-email@yourdomain.com

postgres:
  password: "your-secure-postgres-password"

sonarqube:
  image:
    tag: "26.3.0.120487-community"
  jvmOpts:
    web: "-Xmx1g -Xms512m"
    ce: "-Xmx1g -Xms512m"
    search: "-Xmx512m -Xms512m"
```

### 4. Install the chart

```bash
helm install my-sonarqube sonarqube-helm/ -f my-values.yaml
```

### 5. Access SonarQube

After deployment, SonarQube will be available at `https://sonar.yourdomain.com`.

**Default credentials:** `admin` / `admin` (you will be prompted to change on first login)

## Configuration

### Essential Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `domain` | Your domain name | `example.com` |
| `letsencrypt.email` | Email for Let's Encrypt | `admin@example.com` |
| `sonarqube.image.tag` | SonarQube image tag | `26.3.0.120487-community` |
| `postgres.password` | PostgreSQL DB password | `changeme-postgres-password` |

### Full Configuration Reference

<details>
<summary>Click to expand all parameters</summary>

#### Namespace

| Parameter | Description | Default |
|-----------|-------------|---------|
| `namespace` | Kubernetes namespace | `sonarqube` |
| `createNamespace` | Create namespace | `true` |

#### Domain & SSL

| Parameter | Description | Default |
|-----------|-------------|---------|
| `domain` | Your domain | `example.com` |
| `enableWwwRedirect` | Handle www subdomain | `true` |
| `letsencrypt.email` | Certificate email | `admin@example.com` |
| `letsencrypt.server` | ACME server URL | production |
| `letsencrypt.createClusterIssuer` | Create ClusterIssuer | `true` |
| `letsencrypt.clusterIssuerName` | ClusterIssuer name | `letsencrypt-prod` |

#### SonarQube

| Parameter | Description | Default |
|-----------|-------------|---------|
| `sonarqube.image.repository` | Image repository | `sonarqube` |
| `sonarqube.image.tag` | Image tag | `26.3.0.120487-community` |
| `sonarqube.replicas` | Number of replicas | `1` |
| `sonarqube.resources.requests.memory` | Memory request | `2Gi` |
| `sonarqube.resources.requests.cpu` | CPU request | `500m` |
| `sonarqube.resources.limits.memory` | Memory limit | `4Gi` |
| `sonarqube.resources.limits.cpu` | CPU limit | `2000m` |
| `sonarqube.jvmOpts.web` | Web process JVM opts | `-Xmx1g -Xms512m` |
| `sonarqube.jvmOpts.ce` | CE process JVM opts | `-Xmx1g -Xms512m` |
| `sonarqube.jvmOpts.search` | Search process JVM opts | `-Xmx512m -Xms512m` |
| `sonarqube.timezone` | Timezone | `Europe/Istanbul` |

#### PostgreSQL

| Parameter | Description | Default |
|-----------|-------------|---------|
| `postgres.image.repository` | Image repository | `postgres` |
| `postgres.image.tag` | Image tag | `16` |
| `postgres.database` | Database name | `sonarqube` |
| `postgres.user` | PostgreSQL user | `sonarqube` |
| `postgres.password` | PostgreSQL password | `changeme-postgres-password` |

#### Persistence

| Parameter | Description | Default |
|-----------|-------------|---------|
| `persistence.storageClass` | Storage class | `local-path` |
| `persistence.sonarqube.enabled` | Enable SonarQube persistence | `true` |
| `persistence.sonarqube.size` | SonarQube PVC size | `10Gi` |
| `persistence.postgres.enabled` | Enable Postgres persistence | `true` |
| `persistence.postgres.size` | Postgres PVC size | `10Gi` |

#### Sysctl (Elasticsearch)

| Parameter | Description | Default |
|-----------|-------------|---------|
| `sysctl.enabled` | Enable sysctl init container | `true` |
| `sysctl.vmMaxMapCount` | vm.max_map_count value | `524288` |

#### Health Checks

| Parameter | Description | Default |
|-----------|-------------|---------|
| `healthChecks.sonarqube.enabled` | Enable SonarQube health checks | `true` |
| `healthChecks.sonarqube.initialDelaySeconds` | Initial delay | `120` |
| `healthChecks.sonarqube.path` | Health check endpoint | `/api/system/status` |
| `healthChecks.postgres.enabled` | Enable Postgres health checks | `true` |

</details>

## Usage

### Install

```bash
helm install my-sonarqube sonarqube-helm/ -f my-values.yaml
```

### Upgrade

```bash
helm upgrade my-sonarqube sonarqube-helm/ -f my-values.yaml
```

### Uninstall

```bash
helm uninstall my-sonarqube
```

> **Note**: PersistentVolumeClaims are not deleted automatically. To delete all data:
>
> ```bash
> kubectl delete pvc -l app.kubernetes.io/instance=my-sonarqube -n sonarqube
> ```

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                             Internet                            │
└─────────────────────────────────┬───────────────────────────────┘
                                  │
                                  ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Traefik Ingress (K3s)                        │
│                     + Let's Encrypt TLS                         │
└─────────────────────────────────┬───────────────────────────────┘
                                  │
                                  ▼
┌─────────────────────────────────────────────────────────────────┐
│                    SonarQube Deployment                         │
│              (sonarqube:26.3.0.120487-community)                │
│                                                                 │
│  ┌──────────────────────┐                                       │
│  │  Init: sysctl        │  vm.max_map_count=524288 (privileged) │
│  │  Init: chmod         │  Fix volume permissions               │
│  └──────────────────────┘                                       │
│                                                                 │
│  ┌──────────────────────┐   ┌───────────────────────────────┐   │
│  │   SonarQube Pod      │──▶│  PVC: sonarqube-data (10Gi)   │   │
│  │   Port: 9000         │   │    ├── /data                  │   │
│  │                      │   │    ├── /extensions             │   │
│  │   Web  (-Xmx1g)     │   │    └── /logs                  │   │
│  │   CE   (-Xmx1g)     │   └───────────────────────────────┘   │
│  │   ES   (-Xmx512m)   │                                       │
│  └──────────┬───────────┘                                       │
└─────────────┼───────────────────────────────────────────────────┘
              │
              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    PostgreSQL StatefulSet                       │
│                        (postgres:16)                            │
│                                                                 │
│  ┌──────────────────────┐   ┌───────────────────────────────┐   │
│  │   Postgres Pod       │──▶│  PVC: postgres-data (10Gi)    │   │
│  │   Port: 5432         │   └───────────────────────────────┘   │
│  └──────────────────────┘                                       │
└─────────────────────────────────────────────────────────────────┘
```

## Production Checklist

- [ ] Change `postgres.password` to a strong password
- [ ] Set `domain` to your actual domain
- [ ] Set `letsencrypt.email` to your email
- [ ] Configure DNS A record pointing to your K3s node
- [ ] Ensure node has at least 4GB available RAM
- [ ] Change default admin password after first login
- [ ] Review JVM options based on available resources
- [ ] Consider increasing PVC sizes for large codebases

## Contributing

Contributions are welcome! Please read our [Contributing Guide](CONTRIBUTING.md) for details.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Show Your Support

If this project helped you, please give it a star on GitHub!

## Contact

- GitHub: [@cagatayuresin](https://github.com/cagatayuresin)

---

Made with love for the K3s community