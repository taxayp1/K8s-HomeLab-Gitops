# Multi-Node GitOps Platform

### Automated Kubernetes Infrastructure & Continuous Delivery

> Enterprise-inspired GitOps platform powering a high-availability bare-metal Kubernetes cluster using ArgoCD, self-hosted GitLab CE, Kaniko, CloudNativePG, HashiCorp Vault, and Cloudflare Tunnel — with a full end-to-end CI/CD pipeline deployed to production.

---

## Overview

This repository is the declarative source of truth for all Kubernetes platform components and application deployments running on a six-node high-availability homelab cluster.

The platform follows GitOps principles — all infrastructure and application changes are committed to Git, automatically reconciled by ArgoCD, and continuously converged to desired state. A complete CI/CD pipeline takes code from a developer's machine to a running container in the cluster with zero manual steps after the git push.

The long-term objective is to evolve this into a hybrid Platform Engineering lab spanning on-premises infrastructure and AWS — using the homelab as the data plane and AWS for cloud-native compute workloads.

---

## Architecture

```text
┌─────────────────────────────────────────────────────────────────┐
│                     DEVELOPER WORKFLOW                          │
│                                                                 │
│   VS Code (Windows)                                             │
│        │                                                        │
│        │  git push                                              │
│        ▼                                                        │
│   Private GitLab CE ──────────────────► GitHub (Push Mirror)   │
│   gitlab.taxayp.com   (portfolio)       github.com/taxayp1     │
│        │                                                        │
│        │  CI pipeline triggers                                  │
│        ▼                                                        │
│   GitLab Runner (Kubernetes executor)                           │
│        │                                                        │
│        ├── Stage 1: build-image                                 │
│        │     Kaniko builds Docker image (rootless, no DinD)     │
│        │     Pushes SHA-tagged image to self-hosted registry    │
│        │     registry.taxayp.com/taxayp/sportsodds:SHA          │
│        │                                                        │
│        └── Stage 2: update-manifests                           │
│              Auto-commits new image tag to this GitOps repo     │
│              (apps/sportsodds/deployment.yaml + cronjob.yaml)   │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             │  ArgoCD detects manifest change
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│              HIGH AVAILABILITY KUBERNETES CLUSTER               │
│           6 nodes · 3 HP SFF boxes · ~104GB RAM                 │
│                                                                 │
│  ┌─────────────┐   ┌─────────────┐   ┌─────────────────────┐  │
│  │  ArgoCD     │   │  Secrets    │   │  Networking         │  │
│  │  GitOps     │   │  Vault+ESO  │   │  Cilium+Hubble CNI  │  │
│  │  reconcile  │   │  injection  │   │  MetalLB+ingress    │  │
│  └──────┬──────┘   └─────────────┘   └─────────────────────┘  │
│         │                                                       │
│         ▼  deploys                                              │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                   SportsOdds App                         │  │
│  │                                                          │  │
│  │  Deployment (Node.js)  ◄──── CloudNativePG (PostgreSQL)  │  │
│  │         +                         +                      │  │
│  │  CronJob (odds fetch)        S3 automated backups        │  │
│  │  runs every 12 hours                                     │  │
│  └──────────────────────────┬───────────────────────────────┘  │
│                             │                                   │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Observability: Prometheus · Grafana · Loki · Promtail   │  │
│  │  Storage: Longhorn distributed block storage             │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             │  Cloudflare Tunnel (outbound only)
                             │  Home IP never exposed · Zero port forward
                             ▼
                    ┌─────────────────┐
                    │  Public Users   │
                    │  sportsodds     │
                    │  .taxayp.com    │
                    └─────────────────┘
```

---

## Private GitOps + Public Portfolio Mirror

Production source of truth lives on the internal self-hosted GitLab CE instance. ArgoCD syncs directly from GitLab. GitLab push-mirrors all commits to this public GitHub repository for portfolio visibility.

```text
Developer  →  Private GitLab  →  ArgoCD  →  Cluster
                    │
                    └──────────►  GitHub (this repo, push mirror)
```

---

## Repository Structure

```text
.
├── apps/
│   ├── gitlab/             # Self-hosted GitLab CE (Helm chart, CNPG, Minio)
│   ├── nextcloud/          # Nextcloud file storage
│   └── sportsodds/         # Sports odds comparison app (full CI/CD)
│       ├── namespace.yaml
│       ├── deployment.yaml
│       ├── cronjob.yaml
│       ├── service.yaml
│       ├── ingress.yaml
│       ├── cnpg-cluster.yaml
│       └── vault-bridge.yaml
│
├── clusters/
│   └── homelab/            # Root bootstrap — App of Apps
│
└── infrastructure/
    ├── argocd/
    ├── ingress-nginx/
    ├── loadbalancer/
    ├── logging/
    ├── monitoring/
    └── security/
```

---

## Technology Stack

### Kubernetes Platform
- Kubernetes (kubeadm) · Kube-VIP (HA control plane)
- ArgoCD · App of Apps GitOps pattern
- MetalLB · ingress-nginx
- Cilium CNI + Hubble (eBPF networking + observability)
- cert-manager (Cloudflare DNS-01 TLS)
- Cloudflare Tunnel (zero-trust public exposure)

### CI/CD
- Self-hosted GitLab CE (source, registry, pipelines)
- GitLab CI + Kaniko (rootless container builds)
- Automated image tag promotion via GitOps commit-back pattern
- ArgoCD automated sync + selfHeal

### Data & Secrets
- CloudNativePG (PostgreSQL operator, S3 backups, PITR)
- HashiCorp Vault (secrets storage)
- External Secrets Operator (automatic k8s Secret sync)

### Observability
- Prometheus + Grafana (metrics + dashboards)
- Loki + Promtail (log aggregation)

### Storage & Infrastructure
- Longhorn (distributed block storage)
- Proxmox VE (hypervisor layer)
- Terraform (infrastructure as code)

---

## Hardware

| Nodes | Role | RAM |
|---|---|---|
| cp-01, cp-02, cp-03 | Control plane | ~8–9 GB allocated each |
| wk-01, wk-02, wk-03 | Workers | ~21–24 GB allocated each |

**Physical hosts:** 3 HP EliteDesk SFF boxes · ~104GB aggregate RAM

---

## Infrastructure Dependency

Underlying VM infrastructure is provisioned separately with Terraform:

**[Proxmox-Terraform-Infra-For-K8s](https://github.com/taxayp1/Proxmox-Terraform-Infra-For-K8s)**

Provisions: Proxmox VMs · CPU/RAM allocation · Static IPs · Base OS · Kubernetes node readiness

---

## Live Application

**[sportsodds.taxayp.com](https://sportsodds.taxayp.com)** — Australian sports betting odds comparison (Cricket, AFL, NRL, Tennis, UFC). The only publicly exposed service — all others are internal only.

---

## Roadmap

- Scraper bot workloads (Playwright-based, per-bookmaker, wave-concurrency)
- Hybrid cloud: AWS EKS compute → homelab CloudNativePG data plane
- HA monitoring alerts and PagerDuty/webhook integration
- Trivy vulnerability scanning in CI pipeline
- Network policies and zero-trust security hardening
- Multi-cluster ArgoCD management

---

## Purpose

A continuously evolving Platform Engineering lab built to simulate real-world practices: GitOps, Kubernetes, infrastructure as code, observability, secrets management, and cloud-native delivery. Built alongside CKA, AWS SAA, AWS DVA, and Terraform Associate certifications — with practical implementation going well beyond certification scope.