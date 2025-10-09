# 🌐 Homelab — Network Plan & Configuration

The **Network Plan** describes how my Homelab cluster is connected, how each node receives its IP address and hostname, and how the design will evolve over time.  
It serves both as a technical reference and as documentation of my decision-making process.

---

## 🏠 Phase 1 — Current Network Setup

The Homelab currently runs inside my home network managed by a **TP-Link Deco mesh system**.  
All devices share a single flat subnet for simplicity and stability during the initial K3s deployment.

| Setting | Value |
|----------|--------|
| Router | TP-Link Deco (M5 mesh system) |
| Subnet | `192.168.68.0/24` |
| Gateway | `192.168.68.1` |
| DNS | Cloudflare `1.1.1.1`, Google `8.8.8.8` |
| DHCP | Enabled (Deco) |
| VLANs | None — flat LAN topology |

💡 *The goal in this phase is simplicity: a predictable, stable environment for initial cluster builds and testing.*

---

## 🧾 Static IP & Hostname Overview

Each Ubuntu node uses a manually configured static IP via **Netplan** (`/etc/netplan/50-cloud-init.yaml`).  
Hostnames follow a consistent pattern for clarity inside the cluster.

| Role | Hostname | IP Address | Notes |
|------|-----------|-------------|--------|
| Controller | `controller` | `192.168.68.152` | Main K3s control plane |
| Worker 1 | `worker-1` | `192.168.68.150` | K3s worker node |
| Worker 2 | `worker-2` | `192.168.68.151` | K3s worker node |
| NAS | `ugreen-nas` | `192.168.68.50` | Ugreen NASync DXP2800 (NFS backend) |
| Gateway | `deco-router` | `192.168.68.1` | TP-Link Deco mesh router |

All nodes have identical `/etc/hosts` entries for internal name resolution, allowing commands such as `ping controller` or `ssh worker-1` to work directly.

---

## ⚙️ Configuration Highlights

### Static Networking
Each node defines its IP address, gateway, and DNS servers locally using Netplan.  
Cloud-Init network management is disabled to prevent overwriting settings.

### Hostnames
- Controller → `controller`  
- Worker 1 → `worker-1`  
- Worker 2 → `worker-2`

This convention keeps `kubectl get nodes` output clean and readable.

### Topology Diagram
```text
TP-Link Deco Router (192.168.68.1)
│
├── controller  192.168.68.152
├── worker-1    192.168.68.150
├── worker-2    192.168.68.151
└── ugreen-nas  192.168.68.50
Flat LAN 192.168.68.0/24 — simple, quiet, and ideal for early experimentation.

🔐 Security Notes
SSH access allowed only inside the local LAN (192.168.68.0/24)

NFS exports on the NAS restricted to cluster IP addresses

No inbound ports exposed to the public internet

Deco cloud remote management disabled

SSH key-based authentication enforced on all nodes

🔮 Phase 2 — Future Network Segmentation
As the Homelab grows, the plan is to migrate from the current flat layout to a segmented design using VLANs or separate subnets.
This will enable isolation between home, lab, and storage traffic and better emulate production environments.

VLAN	Subnet	Purpose
10	192.168.10.0/24	Home & IoT devices
20	192.168.20.0/24	Homelab / Kubernetes nodes
30	192.168.30.0/24	NAS and storage traffic
99	192.168.99.0/24	Management & monitoring

Benefits

Clear separation between workloads

Easier firewall and access-control policies

Reduced broadcast traffic

Foundation for pfSense / UniFi / MikroTik integration

✅ Summary
Current phase: single flat subnet 192.168.68.0/24 on Deco mesh

Static IPs configured locally via Netplan

Hostnames: controller, worker-1, worker-2, ugreen-nas

Secure, quiet, and predictable environment for K3s

Next step: introduce VLAN segmentation and dedicated management network
