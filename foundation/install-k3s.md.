# ☸️ K3s Cluster Installation — Multi-node Setup

This document describes the step-by-step process of setting up a **multi-node K3s cluster** inside the Homelab environment.

---

## 🧠 Overview

K3s is a lightweight Kubernetes distribution optimized for small environments such as homelabs, edge devices, and IoT.  
In this setup, one node acts as the **controller (server)** and two nodes act as **workers (agents)**.

| Role | Hostname | IP Address | OS | Description |
|------|-----------|-------------|----|--------------|
| Controller | `controller` | `192.168.68.152` | Ubuntu Server 24.04 LTS | Runs the K3s control-plane |
| Worker 1 | `worker-1` | `192.168.68.150` | Ubuntu Server 24.04 LTS | K3s agent node |
| Worker 2 | `worker-2` | `192.168.68.151` | Ubuntu Server 24.04 LTS | K3s agent node |

---

## ⚙️ Step 1 — Install K3s on the Controller

SSH into the controller:
```bash
ssh controller
