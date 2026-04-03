# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-04-03

### Added

- Initial release of Awesome K3s SonarQube Helm chart
- SonarQube Community Edition v26.3.0.120487 support
- PostgreSQL 16 StatefulSet with persistent storage
- Automatic Let's Encrypt SSL via cert-manager
- Traefik ingress integration for K3s
- Privileged init container for Elasticsearch sysctl requirements
- Configurable values for:
  - Domain and www redirect
  - Let's Encrypt email and server
  - SonarQube and PostgreSQL image tags
  - Database credentials
  - JVM options (web, ce, search)
  - Resource limits and requests
  - Persistence storage size
- Production-ready health checks (startup, liveness, readiness)
- Comprehensive documentation

### Security

- Secrets stored as Kubernetes Secrets
- Security contexts for pods
- TLS termination at ingress level
- Non-root user for SonarQube container
