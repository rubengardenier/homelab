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
```bash
curl -sfL https://get.k3s.io | sh -s - --write-kubeconfig-mode 644
```

Check that it’s running:
```bash
sudo systemctl status k3s
```

Get the node token (needed for worker registration):
```bash
sudo cat /var/lib/rancher/k3s/server/node-token
```

Example token:
```
K10870e4c67eae3abac52f3f6e2b3db93a86dc6ccdd7f3d8a2a7c3e9cc6c39c7a::server:1c32a8f3d4b29b1a1d0aafc3e8f1b8f3
```

Verify the initial controller node:
```bash
sudo kubectl get nodes -o wide
```

## ⚙️ Step 2 — Join the Worker Nodes
Repeat the following on worker-1 and worker-2:
```bash
ssh worker-1
```

Install the K3s agent, replacing the token and IP with your own:
```bash
curl -sfL https://get.k3s.io | K3S_URL=https://192.168.68.152:6443 K3S_TOKEN=<your-node-token> sh -
```

Check the agent status:
```bash
sudo systemctl status k3s-agent
```
Repeat the same for worker-2.

<br>

## 🧩 Step 3 — Verify Cluster Status
Back on the controller, confirm that all nodes are active and connected:
```bash
sudo kubectl get nodes -o wide
```
Output:
| NAME        | STATUS | ROLES                 | AGE  | VERSION       | INTERNAL-IP    | EXTERNAL-IP | OS-IMAGE             | KERNEL-VERSION   | CONTAINER-RUNTIME          |
|--------------|--------|-----------------------|------|----------------|----------------|--------------|----------------------|------------------|-----------------------------|
| controller  | Ready  | control-plane,master  | 3m18s | v1.33.5+k3s1  | 192.168.68.152 | <none>       | Ubuntu 24.04.3 LTS  | 6.8.0-85-generic | containerd://2.1.4-k3s1     |
| worker-1    | Ready  | <none>                | 66s   | v1.33.5+k3s1  | 192.168.68.150 | <none>       | Ubuntu 24.04.3 LTS  | 6.8.0-85-generic | containerd://2.1.4-k3s1     |
| worker-2    | Ready  | <none>                | 55s   | v1.33.5+k3s1  | 192.168.68.151 | <none>       | Ubuntu 24.04.3 LTS  | 6.8.0-85-generic | containerd://2.1.4-k3s1     |


## 🧩 Step 4 — Set Up Kubeconfig Access on Your Laptop

Once K3s is installed on the controller, you can manage the cluster remotely from your laptop.  
To do this, you’ll copy the `kubeconfig` file from your controller to your local machine  
and configure your environment so `kubectl` always knows where to find it.

---

### 🪣 1. Copy the kubeconfig from the controller

Run this **on your laptop**, not on the controller:

```bash
scp username@192.168.68.152:~/.kube/config ~/.kube/config-homelab
```

Enter your SSH password when prompted.
This copies the cluster’s kubeconfig to your local .kube directory.

Verify it exists:
```bash
ls -l ~/.kube/
```

Expected output:
```bash
config-homelab
```

### 🧭 2. Update the API server address

By default, the kubeconfig file uses 127.0.0.1 as the cluster API address.
You need to replace it with the controller’s LAN IP so your laptop can reach it.

Edit the file:
```bash
nano ~/.kube/config-homelab
```

Find the line:
```bash
server: https://127.0.0.1:6443
```

Change it to:
```bash
server: https://192.168.68.152:6443
```

Save (Ctrl + O, Enter) and exit (Ctrl + X).

### ⚙️ 3. Set KUBECONFIG permanently (recommended)
To make this configuration the default every time you open a terminal,
add this line to your .bashrc file:
```bash
echo 'export KUBECONFIG=$HOME/.kube/config-homelab' >> ~/.bashrc
source ~/.bashrc
```

✅ Now you can use kubectl directly without specifying the kubeconfig file:
``` bash
kubectl get nodes
```




























