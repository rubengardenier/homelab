
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

💡 For simplicity, all IPs are currently reserved via DHCP or manually configured on each Ubuntu node.

---

## 🧾 Static IP Assignments

| Role | Hostname | IP Address | OS / Notes |
|------|-----------|-------------|-------------|
| Controller | `controller` | `192.168.68.152` | Ubuntu Server 24.04 LTS |
| Worker 1 | `worker-1` | `192.168.68.150` | Ubuntu Server 24.04 LTS |
| Worker 2 | `worker-2` | `192.168.68.151` | Ubuntu Server 24.04 LTS |
| NAS | `ugreen-nas` | `192.168.68.50` | Ugreen NASync DXP2800 (NFS backend) |
| Gateway | `deco-router` | `192.168.68.1` | TP-Link Deco system |

All nodes have static IPs defined either through **DHCP reservation** in the Deco app  
or via **Netplan** on each Ubuntu node.

---

## ⚙️ Network Configuration on Ubuntu Nodes

Each Ubuntu node uses a static IP configuration defined via Netplan:

Example (for `worker-1`):

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

pply configuration:

sudo netplan apply

Hostname Configuration

Each node has a descriptive hostname:

Node	Hostname
Controller	controller
Worker 1	worker-1
Worker 2	worker-2

To set the hostname:

sudo hostnamectl set-hostname worker-1

🗺️ Topology Overview
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
(K3s master)       (K3s node)          (K3s node)            (NFS storage)

🔐 Security Notes

SSH access allowed only within LAN (192.168.68.0/24)

NAS NFS shares restricted to IPs of controller and workers

No inbound ports exposed to the internet

Deco cloud / remote management disabled

Strong SSH key-based authentication on all nodes

🔮 Phase 2 — Future Network Segmentation

As the Homelab expands, the plan is to migrate from a flat network
to a segmented design with VLANs and dedicated subnets.

VLAN	Subnet	Purpose
10	192.168.10.0/24	Home & IoT devices
20	192.168.20.0/24	Homelab / Kubernetes nodes
30	192.168.30.0/24	NAS and storage traffic
99	192.168.99.0/24	Management & monitoring
Benefits of segmentation

Better isolation between home, lab, and storage networks

Easier firewall and access control configuration

Reduced broadcast traffic

More production-like environment for learning and simulation

Implementation will require a managed switch or router (e.g., UniFi, MikroTik, or pfSense).

✅ Summary

The current setup uses a single 192.168.68.0/24 subnet managed by TP-Link Deco

All nodes and the NAS have static or reserved IPs

The design prioritizes simplicity and stability during initial K3s deployment

A future VLAN-based topology will introduce isolation and improved security
