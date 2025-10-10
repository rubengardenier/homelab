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
```

Install the K3s server:
```
curl -sfL https://get.k3s.io | sh -
```

Check that it’s running:
```
sudo systemctl status k3s
```

Get the node token (needed for worker registration):
```
sudo cat /var/lib/rancher/k3s/server/node-token
```

Example token:
```
K10870e4c67eae3abac52f3f6e2b3db93a86dc6ccdd7f3d8a2a7c3e9cc6c39c7a::server:1c32a8f3d4b29b1a1d0aafc3e8f1b8f3
```

Verify the initial controller node:
```
sudo kubectl get nodes -o wide
```


