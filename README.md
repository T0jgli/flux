# Kubernetes GitOps Homelab

GitOps repository for a K3s homelab cluster, managed entirely with [Flux CD](https://fluxcd.io/).

## Overview

This repository defines the desired state of a single-node K3s cluster. Flux continuously reconciles the cluster state against Git, ensuring declarative, version-controlled infrastructure and application delivery.

## Repository Structure

```
├── clusters/jatek/       # Cluster entrypoint — Flux Kustomizations that reference apps & infra
├── apps/                  # Application workloads
│   ├── helm/             # Helm-based apps (sealed-secrets, zot, rustfs, fluentbit)
│   └── ...               # Kustomize-based apps
└── infrastructure/       # Core cluster infrastructure
```

## Infrastructure

| Component                     | Purpose                                          |
| ----------------------------- | ------------------------------------------------ |
| **Envoy Gateway**             | Ingress via Kubernetes Gateway API               |
| **cert-manager**              | TLS certificates (Let's Encrypt + internal CA)   |
| **External Secrets Operator** | Secrets management via Bitwarden Secrets Manager |
| **CloudNative PG**            | PostgreSQL operator                              |
| **Velero**                    | Cluster backup & restore                         |
| **Falco**                     | Runtime security monitoring                      |
| **Fluent Bit**                | Log aggregation                                  |
| **CoreDNS**                   | Custom DNS configuration                         |
| **System Upgrade Controller** | Automated K3s upgrades                           |

## Applications

| App              | Description                     |
| ---------------- | ------------------------------- |
| **Keycloak**     | Identity & access management    |
| **n8n**          | Workflow automation             |
| **PostgreSQL**   | Primary database (CNPG managed) |
| **MSSQL**        | Azure SQL Edge                  |
| **Portainer**    | Container management UI         |
| **pgAdmin**      | PostgreSQL administration       |
| **Uptime Kuma**  | Status page & monitoring        |
| **Timetagger**   | Time tracking                   |
| **websocket-dc** | Custom WebSocket application    |
| **musicbot**     | Discord music bot               |
| **Zot**          | OCI container registry          |

## Key Features

- **Full GitOps** — all cluster state defined in Git, reconciled by Flux
- **External Secrets** — secrets synced from Bitwarden Secrets Manager via ESO
- **Image Automation** — Flux automatically updates image tags and commits to `main`
- **Gateway API** — modern Kubernetes-native ingress with Envoy
- **Network Policies** — microsegmentation across namespaces
- **Discord Notifications** — Flux alerts sent to Discord on reconciliation events
- **Automated Upgrades** — K3s node upgrades managed declaratively

## Prerequisites

- K3s cluster
- Flux CLI installed
- Network access to GitHub and the private container registry
- Bitwarden Secrets Manager access token (for secret synchronization)
- Cloudflare API token (for DNS-01 certificate challenges)
- Discord webhook URL (for alerts)

## Secrets

All secrets are managed via [External Secrets Operator](https://external-secrets.io/) backed by [Bitwarden Secrets Manager](https://bitwarden.com/products/secrets-manager/).

A `ClusterSecretStore` connects to Bitwarden via an in-cluster SDK server. Each application defines an `ExternalSecret` resource that references secrets by their Bitwarden ID, and ESO syncs them into native Kubernetes Secrets automatically.
