# Multi-Node GitOps Platform

### Automated Kubernetes Infrastructure & Continuous Delivery

> Enterprise-inspired GitOps platform powering a high-availability bare-metal Kubernetes cluster using Argo CD, GitLab CE, Kustomize, and cloud-native technologies.

---

# Overview

This repository serves as the declarative source for Kubernetes platform components and application deployments running on my six-node high-availability homelab Kubernetes cluster.

The platform follows GitOps principles where infrastructure and application changes are committed to Git, automatically synchronized by Argo CD, and continuously reconciled against the desired cluster state.

The long-term objective is to evolve this environment into a hybrid Platform Engineering lab spanning both on-premises infrastructure and AWS.

---

# Architecture

```text
                    Developer
                        │
                        ▼
                Private GitLab CE
          (Internal Source of Truth)
                │              │
                │              └──────────────► Public GitHub
                │                   (Push Mirror for Portfolio)
                ▼
              Argo CD
       (Continuously Pulls from GitLab)
                │
                ▼
 High Availability Kubernetes Cluster
      ┌──────────┬─────────────┐
      │          │             │
Infrastructure Applications Monitoring
```

---

# Platform Overview

Current platform consists of:

* 3 Kubernetes Control Plane nodes
* 3 Kubernetes Worker nodes
* High Availability bare-metal Kubernetes cluster
* GitOps deployment model with Argo CD
* Kustomize configuration management
* Self-hosted GitLab CE
* MetalLB load balancer
* NGINX Ingress Controller
* Longhorn distributed storage
* Prometheus monitoring
* Grafana dashboards
* Loki centralized logging
* HashiCorp Vault
* External Secrets Operator
* cert-manager certificate automation

---

# Private GitOps Engine & Public Portfolio Mirror

To emulate an enterprise GitOps workflow, the production source of truth is hosted on an internally deployed GitLab CE instance running inside the Kubernetes environment.

Argo CD continuously synchronizes cluster state directly from the internal GitLab repository.

GitLab automatically push-mirrors all commits to this public GitHub repository, allowing the project to remain publicly accessible while preserving an internal-first GitOps workflow.

```text
Developer
     │
     ▼
Private GitLab
     │
     ├────────────► Argo CD
     │               (Pull Model)
     │
     └────────────► GitHub
                 (Push Mirror)
```

---

# Repository Structure

```text
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

## Kubernetes Platform

* Kubernetes
* Argo CD
* Kustomize
* NGINX Ingress Controller
* MetalLB

## CI/CD

* GitLab CE
* GitOps

> GitLab CI/CD integration is currently under development.

## Observability

* Prometheus
* Grafana
* Loki

## Security

* HashiCorp Vault
* External Secrets Operator
* cert-manager

## Storage

* Longhorn

## Infrastructure

* Proxmox VE
* Terraform
* Ubuntu Server

---

# Infrastructure Dependency

This repository manages only Kubernetes-native resources.

The underlying infrastructure is provisioned separately using Terraform.

Infrastructure Repository:

**Proxmox Kubernetes Infrastructure**

https://github.com/taxayp1/Proxmox-Terraform-Infra-For-K8s

That repository provisions:

* Proxmox virtual machines
* CPU & Memory allocation
* Static IP addressing
* Base operating system
* Kubernetes node infrastructure

Once Kubernetes becomes operational, this GitOps repository bootstraps the complete platform.

---

# Current GitOps Workflow

```text
Infrastructure Change
or
Application Change

        │

Commit

        │

Push to Private GitLab

        │

Argo CD detects change

        │

Synchronizes manifests

        │

Kubernetes reconciliation

        │

Desired State Achieved
```

---

# Current Features

* High Availability Kubernetes Cluster
* GitOps deployment with Argo CD
* App-of-Apps architecture
* Self-hosted GitLab CE
* Kustomize-based deployments
* MetalLB load balancing
* NGINX Ingress Controller
* Longhorn distributed storage
* Prometheus monitoring
* Grafana dashboards
* Loki centralized logging
* Secrets management
* Certificate automation

---

# Roadmap

## CI/CD

* GitLab CI/CD pipeline
* Automated container image builds
* Trivy vulnerability scanning
* Automated image publishing

## Kubernetes Platform

* Helm-based application deployments
* Enhanced monitoring dashboards
* Kubernetes autoscaling
* Gateway API evaluation

## Hybrid Cloud (AWS)

* Amazon EKS
* Amazon ECR
* Amazon RDS
* Amazon S3
* AWS CloudWatch
* Hybrid Kubernetes (Homelab + AWS)
* Multi-cluster Argo CD

## Applications

* OddsJunction

  * React frontend
  * API backend
  * GitLab CI/CD
  * GitOps deployment
  * AWS deployment

---

# Purpose

This repository serves as my continuously evolving Platform Engineering laboratory.

Rather than focusing solely on certification objectives, the platform is designed to simulate real-world engineering practices using GitOps, Kubernetes, infrastructure as code, observability, security, and cloud-native technologies.

As the platform evolves, it will expand from an on-premises Kubernetes environment into a hybrid cloud architecture integrating AWS services while maintaining GitOps as the single operational model.
