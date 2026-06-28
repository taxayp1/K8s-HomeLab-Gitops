# Multi-Node GitOps Platform
### Automated Kubernetes Infrastructure & Continuous Delivery

> Enterprise-inspired GitOps platform powering a high-availability bare-metal Kubernetes cluster using Argo CD, GitLab, Kustomize, and cloud-native tooling.

---

## Overview

This repository serves as the declarative source for all Kubernetes platform components and application deployments running on my six-node high-availability homelab Kubernetes cluster.

The platform follows GitOps principles where every infrastructure and application change is committed to Git, automatically synchronized by Argo CD, and continuously reconciled against the desired cluster state.

The goal of this project is to simulate production Platform Engineering practices while continuously expanding into hybrid cloud deployments with AWS.

---

# Architecture

```
                        Developer
                            │
                            ▼
                  Private GitLab CE
                 (Self-Hosted Source of Truth)
                            │
                     Push Mirror
                            │
                            ▼
                  Public GitHub Repository
                            │
                            ▼
                        Argo CD
                            │
               Declarative GitOps Sync
                            │
                            ▼
          High Availability Kubernetes Cluster
         ┌─────────────┬──────────────┐
         │             │              │
   Infrastructure    Applications   Monitoring
```

---

# Platform Overview

Current platform consists of:

- 3 Control Plane Nodes
- 3 Worker Nodes
- Bare-metal Kubernetes
- GitOps-driven deployments
- Kustomize configuration management
- Self-hosted GitLab
- Argo CD continuous delivery
- MetalLB load balancing
- NGINX Ingress Controller
- Prometheus monitoring
- Grafana dashboards
- Loki log aggregation
- Vault & External Secrets
- cert-manager certificate automation

---

# Private GitOps Engine & Public Portfolio Mirror

To emulate an enterprise GitOps workflow, the production source of truth is hosted on an internally deployed GitLab CE instance running inside the Kubernetes cluster.

Argo CD continuously synchronizes cluster state directly from GitLab.

GitLab automatically push-mirrors all commits to this public GitHub repository, allowing the project to remain publicly viewable while preserving an internal-first GitOps workflow.

```
Developer
      │
      ▼
Private GitLab
      │
      ├──► Argo CD
      │
      └──► Push Mirror
                │
                ▼
        Public GitHub
```

---

# Repository Structure

```
.
├── apps/
│   ├── gitlab/
│   ├── nextcloud/
│   └── whoami/
│
├── clusters/
│   └── homelab/
│       ├── argo-apps.yaml
│       └── kustomization.yaml
│
├── infrastructure/
│   ├── argocd/
│   ├── ingress-nginx/
│   ├── loadbalancer/
│   ├── logging/
│   ├── monitoring/
│   └── security/
│
└── kustomization.yaml
```

---

# Technology Stack

## Kubernetes

- Kubernetes
- Kustomize
- Argo CD
- NGINX Ingress
- MetalLB

## CI/CD

- GitLab CE
- GitLab Runner
- GitOps

## Observability

- Prometheus
- Grafana
- Loki

## Security

- HashiCorp Vault
- External Secrets Operator
- cert-manager

## Infrastructure

- Proxmox VE
- Terraform
- Ubuntu Server
- Longhorn (planned)

---

# Infrastructure Dependency

This repository manages only Kubernetes-native resources.

The underlying infrastructure is provisioned separately using Terraform.

Infrastructure Repository:

**Proxmox Kubernetes Infrastructure**
https://github.com/taxayp1/Proxmox-Terraform-Infra-For-K8s

That repository provisions:

- Proxmox virtual machines
- CPU & Memory allocation
- Networking
- Static IP assignments
- Kubernetes nodes

Once Kubernetes becomes operational, this repository bootstraps the complete platform using GitOps.

---

# GitOps Workflow

```
Code Change

      │

Git Commit

      │

Private GitLab

      │

Argo CD detects change

      │

Cluster Sync

      │

Kubernetes Reconciliation

      │

Desired State Achieved
```

---

# Current Features

- High Availability Kubernetes Cluster
- GitOps Platform using Argo CD
- App-of-Apps deployment model
- Self-hosted GitLab CE
- MetalLB load balancing
- NGINX Ingress Controller
- Prometheus monitoring
- Grafana dashboards
- Loki centralized logging
- Certificate automation
- Secrets management
- Declarative Kustomize deployments

---

# Roadmap

## Platform

- GitLab CI/CD pipeline
- Helm deployment support
- Trivy image scanning
- SBOM generation
- Policy enforcement
- Backup automation

## Cloud

- AWS EKS deployment
- Amazon ECR integration
- Amazon RDS
- AWS CloudWatch
- Hybrid Kubernetes (Homelab + AWS)
- Multi-cluster Argo CD

## Security

- Cilium
- Network Policies
- Image Signing
- Runtime Security
- Falco

---

# Purpose

This project serves as my continuously evolving Platform Engineering laboratory.

Rather than focusing solely on certification objectives, the platform is designed to simulate real-world enterprise environments where infrastructure, continuous delivery, observability, security, and cloud services are managed using modern cloud-native practices.

The long-term objective is to evolve this platform into a hybrid Kubernetes environment spanning both on-premises infrastructure and AWS.