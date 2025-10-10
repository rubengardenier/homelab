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

Install the K3s server (with readable kubeconfig for your user):
```
curl -sfL https://get.k3s.io | sh -s - --write-kubeconfig-mode 644
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

## ⚙️ Step 2 — Join the Worker Nodes
Repeat the following on worker-1 and worker-2:
```
ssh worker-1
```

Install the K3s agent, replacing the token and IP with your own:
```
curl -sfL https://get.k3s.io | K3S_URL=https://192.168.68.152:6443 K3S_TOKEN=<your-node-token> sh -
```

Check the agent status:
```
sudo systemctl status k3s-agent
```
Repeat the same for worker-2.

<br>

## 🧩 Step 3 — Verify Cluster Status
Back on the controller, confirm that all nodes are active and connected:
```
sudo kubectl get nodes -o wide
```
Output:
| NAME        | STATUS | ROLES                 | AGE  | VERSION       | INTERNAL-IP    | EXTERNAL-IP | OS-IMAGE             | KERNEL-VERSION   | CONTAINER-RUNTIME          |
|--------------|--------|-----------------------|------|----------------|----------------|--------------|----------------------|------------------|-----------------------------|
| controller  | Ready  | control-plane,master  | 3m18s | v1.33.5+k3s1  | 192.168.68.152 | <none>       | Ubuntu 24.04.3 LTS  | 6.8.0-85-generic | containerd://2.1.4-k3s1     |
| worker-1    | Ready  | <none>                | 66s   | v1.33.5+k3s1  | 192.168.68.150 | <none>       | Ubuntu 24.04.3 LTS  | 6.8.0-85-generic | containerd://2.1.4-k3s1     |
| worker-2    | Ready  | <none>                | 55s   | v1.33.5+k3s1  | 192.168.68.151 | <none>       | Ubuntu 24.04.3 LTS  | 6.8.0-85-generic | containerd://2.1.4-k3s1     |
