Multi-Node GitOps Platform: Automated Kubernetes Infrastructure & Continuous Delivery

This repository drives the declarative, GitOps-controlled application delivery platform running across a high-availability bare-metal Kubernetes (v1.35) cluster.

🔒 Private GitOps Engine & Public Portfolio Mirror
To emulate a true enterprise, air-gapped environment, the actual source of truth for this cluster is a self-hosted Gitea instance running internally. ArgoCD continuously syncs cluster state directly from the local Gitea server, which is configured with an automated push-mirror to sync all commits to this public GitHub repository for visibility.

Repository Map & Architecture Blueprint
The directory structure separates core system platform layers from stateless application spaces, following cloud-native organizational standards.
---

Repository Map & Architecture Blueprint

The `infrastructure/` directory separates core system platform layers from stateless application spaces, following cloud-native organizational standards.
```text
├.
├── apps/                        # All user and functional business applications
│   ├── gitea/                  # Internal Git Server & repository management
│   ├── nextcloud/              # Heavyweight self-hosted productivity suite
│   └── whoami/                 # Lightweight network diagnostic/debugging tool
├── clusters/
│   └── homelab/                # Target environment bootstrap & App-of-Apps roots
│       ├── argo-apps.yaml      # ArgoCD App-of-Apps pattern controller
│       └── kustomization.yaml  # Base environment cluster overlays
├── infrastructure/              # Base cluster platform engines and utilities
│   ├── argocd/                 # The Continuous Delivery Engine (Self-managed GitOps)
│   ├── ingress-nginx/          # External L7 Routing Layer & Hubble Ingress paths
│   ├── loadbalancer/           # On-Premises bare-metal IP assignment (MetalLB)
│   ├── logging/                # Distributed cluster log aggregation
│   ├── monitoring/             # Metrics pipeline (Prometheus & Grafana dashboards)
│   └── security/               # Zero-Trust secrets & certificates (Vault, ESO, Cert-Manager)
└── kustomization.yaml          # Root cluster orchestration configuration
---

Infrastructure Lifecycle & Dependencies

This platform is split into two distinct, declarative layers. Before deploying this GitOps repository, the underlying bare-metal virtualization, virtual machines, and networking topology must be provisioned.

Infrastructure-as-Code Layer: [Proxmox Core Virtualization & Terraform Provisioning](https://github.com/taxayp1/Proxmox-Terraform-Infra-For-K8s.git) — This repository handles the creation of the 3 contorlplane nodes, 3 worker nodes, CPU/RAM allocation, and static IP mapping inside the Proxmox VE environment.

GitOps Platform Layer: (This Repository) — Handles cluster-native system applications, secrets automation, and routing once Kubernetes is online.
