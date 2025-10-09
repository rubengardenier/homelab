# 🌐 Network Plan

This document describes the current and future network design of my Homelab cluster.  
It defines how all nodes, storage, and services connect within my home environment.

---

## 🏠 Phase 1 — Current Network Setup

At this stage, the Homelab runs inside my existing **TP-Link Deco mesh WiFi system**.  
All devices share a single subnet managed by the Deco router.

| Setting | Value |
|----------|--------|
| Router | TP-Link Deco |
| Subnet | `192.168.68.0/24` |
| Gateway | `192.168.68.1` |
| DNS | Cloudflare `1.1.1.1`, Google `8.8.8.8` |
| DHCP | Enabled (Deco) |
| VLANs | None (flat network) |

---

## 🧾 Static IP Assignments

| Role | Hostname | IP Address | OS / Notes |
|------|-----------|-------------|-------------|
| Controller | `controller` | `192.168.68.152` | Ubuntu Server 24.04 LTS |
| Worker 1 | `worker-1` | `192.168.68.150` | Ubuntu Server 24.04 LTS |
| Worker 2 | `worker-2` | `192.168.68.151` | Ubuntu Server 24.04 LTS |
| NAS | `ugreen-nas` | `192.168.68.50` | Ugreen NASync DXP2800 (NFS backend) |
| Gateway | `deco-router` | `192.168.68.1` | TP-Link Deco system |

> **Note:** All Ubuntu nodes use **locally configured static IPs via Netplan** at  
> `/etc/netplan/50-cloud-init.yaml` (Ubuntu 24.04.3 LTS).

---

## ⚙️ Network Configuration on Ubuntu Nodes (Netplan)

Each Ubuntu node uses a static IP configuration defined in `/etc/netplan/50-cloud-init.yaml`.

Edit the file:
```bash
sudo nano /etc/netplan/50-cloud-init.yaml
Example content (for worker-1):

yaml
Code kopiëren
network:
  version: 2
  ethernets:
    enp1s0:
      dhcp4: false
      addresses:
        - 192.168.68.150/24
      routes:
        - to: default
          via: 192.168.68.1
      nameservers:
        addresses: [1.1.1.1, 8.8.8.8]
Apply configuration:

bash
Code kopiëren
sudo netplan apply
Hostname configuration
bash
Code kopiëren
# examples
sudo hostnamectl set-hostname controller
sudo hostnamectl set-hostname worker-1
sudo hostnamectl set-hostname worker-2
(Optional) ensure cloud-init does not overwrite Netplan on reboot:

bash
Code kopiëren
sudo nano /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
yaml
Code kopiëren
network: {config: disabled}
🗺️ Topology Overview
text
Code kopiëren
                    ┌────────────────────────────┐
                    │      TP-Link Deco Router   │
                    │     192.168.68.1 (Gateway) │
                    └────────────┬───────────────┘
                                 │
                ┌────────────────┴────────────────┐
                │           LAN / WiFi            │
                │         192.168.68.0/24         │
                └────────────────┬────────────────┘
                                 │
   ┌────────────────────┬────────────────────┬────────────────────┐
   │                    │                    │                    │
192.168.68.152     192.168.68.150      192.168.68.151       192.168.68.50
controller         worker-1            worker-2              ugreen-nas
(control plane)    (K3s node)          (K3s node)            (NFS storage)
🔐 Security Notes
SSH access allowed only within LAN (192.168.68.0/24)

NAS NFS exports restricted to the three node IPs

No inbound ports exposed to the internet

Deco cloud / remote management disabled

Strong SSH key-based auth on all nodes

🔮 Phase 2 — Future Network Segmentation
Target evolution: move from a flat network to segmented VLANs/subnets.

VLAN	Subnet	Purpose
10	192.168.10.0/24	Home & IoT devices
20	192.168.20.0/24	Homelab / Kubernetes nodes
30	192.168.30.0/24	NAS and storage traffic
99	192.168.99.0/24	Management & monitoring

Benefits

Isolation between home, lab and storage

Cleaner firewalling and access control

Reduced broadcast noise

More production-like topology

(Requires a managed switch/router such as UniFi, MikroTik, or pfSense.)

✅ Summary
Current design: single 192.168.68.0/24 subnet (Deco)

Static IPs are configured via Netplan at /etc/netplan/50-cloud-init.yaml

Simple and stable for initial K3s deployment

Future step: introduce VLANs for isolation and security
