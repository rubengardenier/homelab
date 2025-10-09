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
| Storage Backend | Ugreen NASync DXP2800 | Intel N100 / 8 GB DDR5 / 2 × Samsung 870 EVO 4 TB (RAID 1) + NVMe expansion | Ugreen NAS OS (Linux-based) |

Lightweight, quiet, and power-efficient — perfect for experimentation and 24/7 operation.

---

## ☸️ Goals & Focus Areas

- Build a **multi-node Kubernetes cluster** using [K3s](https://k3s.io)  
- Learn to deploy, manage, and monitor workloads locally  
- Explore key components:
  - **Ingress NGINX**, **MetalLB**, **Cert-Manager**
  - **Persistent storage** (Ugreen NAS via NFS / Longhorn)
  - **Observability** (Prometheus, Grafana, Loki)
- Practice **Infrastructure-as-Code** concepts with:
  - Bicep / Terraform  
  - Ansible / Bash automation  
  - GitHub Actions or Azure DevOps pipelines
- Document every step to make learning repeatable and transparent

---

## 🧱 Foundation

All preparation and setup documentation lives in the [`foundation/`](./foundation/) directory:

- [Network Plan](./foundation/01-network-plan.md)
- [Static IP Configuration](./foundation/02-network-config.md)
- [Node Preparation (tmux, k9s, .bashrc, .vimrc)](./foundation/03-node-preparation.md)
- [Storage & NAS Setup](./infrastructure/storage/00-nas-setup.md)

This section captures all groundwork before cluster deployment —  
from IP planning to tool setup and editor tuning.

---

## 🧩 Repository Structure

```text
homelab/
│
├── foundation/
│   ├── 01-network-plan.md
│   ├── 02-network-config.md
│   ├── 03-node-preparation.md
│   └── README.md
│
├── kubernetes-fundamentals/
│   ├── setup/
│   ├── deployments/
│   └── notes/
│
├── infrastructure/
│   ├── network/
│   ├── storage/
│   └── automation/
│
└── docs/
    ├── lessons-learned.md
    └── roadmap.md
Each section contains step-by-step guides, configurations, and experiments.

🚀 Vision
The goal of this Homelab is to bridge theory and practice —
to gain real-world skills that directly translate into professional cloud engineering, DevOps, and Kubernetes experience.

Eventually, I plan to:

Run production-like services (e.g., Mealie, Gitea, Grafana)

Automate cluster rebuilds end-to-end

Experiment with hybrid Azure + on-prem setups

Develop a custom Arch Linux–based Kubernetes distribution optimized for homelab and learning

“If you can build and break it at home, you can run it in production.” 🧩
