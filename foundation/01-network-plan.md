# 🌐 Homelab Network Plan & Configuration

This document defines the current and future network setup of my Homelab cluster.  
It covers static IP configuration, hostnames, Netplan setup, `/etc/hosts` entries, and planned VLAN segmentation.

---

## 🏠 Phase 1 — Current Network Setup

My Homelab currently runs inside a **TP-Link Deco mesh WiFi system**, using a single subnet managed by the Deco router.

| Setting | Value |
|----------|--------|
| Router | TP-Link Deco |
| Subnet | `192.168.68.0/24` |
| Gateway | `192.168.68.1` |
| DNS | Cloudflare `1.1.1.1`, Google `8.8.8.8` |
| DHCP | Enabled (Deco) |
| VLANs | None (flat network) |

---

## 🧾 Static IP & Hostname Overview

All nodes use **locally configured static IPs** via Netplan (`/etc/netplan/50-cloud-init.yaml`).

| Role | Hostname | IP Address | OS / Notes |
|------|-----------|-------------|-------------|
| Controller | `controller` | `192.168.68.152` | Ubuntu Server 24.04 LTS |
| Worker 1 | `worker-1` | `192.168.68.150` | Ubuntu Server 24.04 LTS |
| Worker 2 | `worker-2` | `192.168.68.151` | Ubuntu Server 24.04 LTS |
| NAS | `ugreen-nas` | `192.168.68.50` | Ugreen NASync DXP2800 (NFS backend) |
| Gateway | `deco-router` | `192.168.68.1` | TP-Link Deco system |

---

## ⚙️ Netplan Configuration (Ubuntu 24.04+)

Static IPs are configured manually on each Ubuntu node using Netplan.  
Edit the file `/etc/netplan/50-cloud-init.yaml`:

### Example configuration (for `worker-1`)

```yaml
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
Apply changes:

bash
Code kopiëren
sudo netplan apply
Optional — disable Cloud-Init overwriting:

bash
Code kopiëren
sudo nano /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
yaml
Code kopiëren
network: {config: disabled}
🧱 Hostname Configuration
Each node has a clear and persistent hostname.

bash
Code kopiëren
# Controller
sudo hostnamectl set-hostname controller

# Worker nodes
sudo hostnamectl set-hostname worker-1
sudo hostnamectl set-hostname worker-2
Verify:

bash
Code kopiëren
hostnamectl
Output:

scss
Code kopiëren
Static hostname: worker-1
📜 /etc/hosts Configuration
Each node includes the same host mappings for internal name resolution.

bash
Code kopiëren
sudo nano /etc/hosts
Content:

javascript
Code kopiëren
127.0.0.1   localhost
127.0.1.1   $(hostname)

192.168.68.152   controller
192.168.68.150   worker-1
192.168.68.151   worker-2
192.168.68.50    ugreen-nas
💡 This allows simple hostname-based access:

bash
Code kopiëren
ping controller
ssh worker-1
🗺️ Network Topology
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
SSH access limited to LAN (192.168.68.0/24)

NAS NFS exports restricted to controller + worker IPs

No inbound ports exposed to the internet

Deco remote management disabled

SSH key authentication enabled on all nodes

Future goal: firewall rules between home and lab traffic

🔮 Phase 2 — Future Network Segmentation
In a future phase, the flat network will evolve into a segmented VLAN setup.

VLAN	Subnet	Purpose
10	192.168.10.0/24	Home & IoT devices
20	192.168.20.0/24	Homelab / Kubernetes nodes
30	192.168.30.0/24	NAS & storage traffic
99	192.168.99.0/24	Management & monitoring

Goals

Isolate homelab workloads from home network

Reduce broadcast and background traffic

Enable more granular firewall and routing control

Prepare for managed networking hardware (pfSense, UniFi, MikroTik)

✅ Summary
Single subnet: 192.168.68.0/24 (Deco)

Static IPs set manually via /etc/netplan/50-cloud-init.yaml

Hostnames: controller, worker-1, worker-2

/etc/hosts consistent across all nodes

Secure, simple, and stable for K3s deployment

Next step: VLAN segmentation and management isolation
