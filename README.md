 Multi-Node GitOps Platform: Automated Kubernetes Infrastructure & Continuous Delivery

This repository drives the declarative, GitOps-controlled application delivery platform running across our high-availability bare-metal Kubernetes (`v1.35`) cluster. Utilizing ArgoCD, the entire platform state is continuously synchronized with this repository, enforcing automated drift correction, centralized secrets management, and declarative service topology.

---

Repository Map & Architecture Blueprint

The `infrastructure/` directory separates core system platform layers from stateless application spaces, following cloud-native organizational standards.
```text
infrastructure/
├── argocd/         # The Continuous Delivery Engine (Manages itself via GitOps)
├── ingress/        # External Routing Layer (Ingress-NGINX)
├── loadbalancer/   # L4 On-Premises Cloud Networking (MetalLB App & Subnet Mapping)
├── logging/        # Log Gathering Engine (Grafana Loki & Promtail DaemonSets)
├── monitoring/     # Metrics Pipeline (Prometheus Operator Stack & Grafana Dashboards)
├── security/       # Zero-Trust Certificates & Cryptographic Secrets (Cert-Manager & External Secrets)
└── storage/        # Multi-Replica Distributed Block Storage Array (Longhorn Engine)
