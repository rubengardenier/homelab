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


## 🧱 Foundation Overview

The **Foundation** section contains all the groundwork completed before deploying the K3s cluster —  
from network design and hostname planning to node setup and NAS configuration.

Each topic builds on the previous one, forming a stable and repeatable base for the Homelab.

| Topic | Description |
|------|-------------|
| [🌐 Network Plan & Configuration](./foundation/network-plan.md) | Defines the IP schema, hostnames, Netplan setup, and `/etc/hosts` synchronization between all nodes. |
| [🧰 Node Preparation](./foundation/node-preparation.md) | Installation and configuration of essential CLI tools such as `tmux`, `k9s`, and shell/editor customization (`.bashrc`, `.vimrc`).
| [☸️ K3s Cluster Installation — Multi-node Setup](./kubernetes-fundamentals/setup/01-install-k3s.md) | Install K3s on the controller and join worker nodes; verify the cluster. |
| [💾 Storage & NAS Setup *(coming soon)*](./infrastructure/storage/README.md) | Configuration of the Ugreen NAS for shared persistent storage (NFS, backups, Longhorn integration). |
| [📈 Observability *(coming soon)*](./kubernetes-fundamentals/monitoring/README.md) | Monitoring stack with Prometheus, Grafana, and Loki. |
| [🧩 Lessons Learned](./docs/lessons-learned.md) | Key takeaways and troubleshooting notes from each stage of the setup. |

💡 *This table serves as your clickable roadmap through the entire Homelab journey.*

---

## 🚀 Vision
The goal of this Homelab is to bridge theory and practice —
to gain real-world skills that directly translate into professional cloud engineering, DevOps, and Kubernetes experience.

Eventually, I plan to:

Run production-like services (e.g., Mealie, Gitea, Grafana)

Automate cluster rebuilds end-to-end

Experiment with hybrid Azure + on-prem setups

Develop a custom Arch Linux–based Kubernetes distribution optimized for homelab and learning


“If you can build and break it at home, you can run it in production.” 🧩
