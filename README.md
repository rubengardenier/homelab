# 🧠 Homelab — My Personal Cloud & Kubernetes Playground

Welcome to my **Homelab** project — a continuously evolving learning environment where I experiment with **cloud-native infrastructure**, **Kubernetes**, and **DevOps automation** on real hardware at home.

This repository serves both as:
- a **knowledge base** of everything I learn, build, and break  
- and as a **public portfolio** showcasing my hands-on experience with modern cloud technologies

---

## ⚙️ Hardware Setup

My cluster is built on a small but efficient setup:

| Role | Device | Specs | OS |
|------|---------|-------|----|
| Controller | HP ProDesk | 4 vCPU / 8 GB RAM / 256 GB SSD | Ubuntu Server 24.04 LTS |
| Worker 1 | HP EliteDesk Mini | 4 vCPU / 8 GB RAM / 256 GB NVMe | Ubuntu Server 24.04 LTS |
| Worker 2 | HP EliteDesk Mini | 4 vCPU / 8 GB RAM / 256 GB NVMe | Ubuntu Server 24.04 LTS |

Lightweight, power-efficient, and perfect for experimentation.

---

## ☸️ Goals & Focus Areas

- Build a **multi-node Kubernetes cluster** using [K3s](https://k3s.io)  
- Learn to deploy, manage, and monitor workloads locally  
- Explore key components:
  - **Ingress NGINX**, **MetalLB**, **Cert-Manager**
  - **Persistent storage** (Longhorn / NFS)
  - **Observability** (Prometheus, Grafana, Loki)
- Practice **Infrastructure-as-Code** concepts with:
  - Bicep / Terraform  
  - Ansible / Bash automation  
  - GitHub Actions or Azure DevOps pipelines
- Document every step to make learning repeatable and transparent

---

## 🧩 Structure

homelab/
│
├── kubernetes-fundamentals/
│ ├── setup/
│ ├── deployments/
│ └── notes/
│
├── infrastructure/
│ ├── network/
│ ├── storage/
│ └── automation/
│
└── docs/
├── lessons-learned.md
└── roadmap.md



Each section contains step-by-step guides, configurations, and experiments.

---

## 🚀 Vision

The goal of this Homelab is to **bridge theory and practice** — to gain real-world skills that directly translate into professional cloud engineering, DevOps, and Kubernetes experience.

Eventually, I plan to:
- Run production-like services (e.g., Mealie, Gitea, Grafana)  
- Automate cluster rebuilds end-to-end  
- Experiment with hybrid Azure + on-prem setups

---

> _“If you can build and break it at home, you can run it in production.”_ 🧩

