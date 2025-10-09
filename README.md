🌐 Homelab — Network Plan & Configuration

This document describes the current and future network design of my Homelab cluster.
It includes static IP configuration, hostnames, Netplan setup, /etc/hosts entries, and plans for VLAN segmentation.

🏠 Phase 1 — Current Network Setup

The Homelab currently runs inside my existing TP-Link Deco mesh WiFi system.
All nodes and devices share a single subnet managed by the Deco router.

Setting	Value
Router	TP-Link Deco
Subnet	192.168.68.0/24
Gateway	192.168.68.1
DNS	Cloudflare 1.1.1.1 / Google 8.8.8.8
DHCP	Enabled (Deco)
VLANs	None (flat network)

💡 The Deco system doesn’t yet support VLANs, so everything runs on one flat network for simplicity.

🧾 Static IP and Hostnames

Each node uses a manually configured static IP (set via Netplan in /etc/netplan/50-cloud-init.yaml).

Role	Hostname	IP Address	Notes
Controller	controller	192.168.68.152	Ubuntu Server 24.04 LTS
Worker 1	worker-1	192.168.68.150	Ubuntu Server 24.04 LTS
Worker 2	worker-2	192.168.68.151	Ubuntu Server 24.04 LTS
NAS	ugreen-nas	192.168.68.50	Ugreen NASync DXP2800 (NFS backend)
Gateway	deco-router	192.168.68.1	TP-Link Deco system
⚙️ Netplan Configuration (on each node)

Edit file: /etc/netplan/50-cloud-init.yaml

Example for worker-1:

 network: version 2
 ethernets: enp1s0
  dhcp4: false
  addresses: 192.168.68.150/24
  routes: to default → via 192.168.68.1
  nameservers: 1.1.1.1, 8.8.8.8

Apply changes: run sudo netplan apply
(optional) disable Cloud-Init overwriting → create /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the line:
 network: {config: disabled}

🧱 Hostnames and /etc/hosts

Set hostnames once per node:
 controller → sudo hostnamectl set-hostname controller
 worker-1 → sudo hostnamectl set-hostname worker-1
 worker-2 → sudo hostnamectl set-hostname worker-2

Verify with hostnamectl.

Update /etc/hosts on each node:

 127.0.0.1 localhost
 127.0.1.1 (hostname)
 192.168.68.152 controller
 192.168.68.150 worker-1
 192.168.68.151 worker-2
 192.168.68.50 ugreen-nas

Now you can ping controller or ssh worker-1 directly.

🗺️ Topology Overview

 TP-Link Deco Router (192.168.68.1)
  │
  ├── controller 192.168.68.152
  ├── worker-1 192.168.68.150
  ├── worker-2 192.168.68.151
  └── ugreen-nas 192.168.68.50

Flat LAN 192.168.68.0/24 — simple and stable for initial K3s deployment.

🔐 Security Notes

SSH allowed only inside LAN (192.168.68.0/24)

NFS exports restricted to controller + workers

No ports exposed to internet

Deco remote management disabled

SSH keys used for authentication

Future: firewall rules for network segmentation

🔮 Phase 2 — Future Network Segmentation
VLAN	Subnet	Purpose
10	192.168.10.0/24	Home & IoT
20	192.168.20.0/24	Homelab / Kubernetes
30	192.168.30.0/24	NAS / Storage
99	192.168.99.0/24	Management & Monitoring

Goals → better isolation, reduced noise, and future integration with pfSense or UniFi.

✅ Summary

Single flat subnet 192.168.68.0/24 (Deco)

Static IPs set manually via Netplan

Hostnames controller / worker-1 / worker-2

Consistent /etc/hosts across all nodes

Secure and ready for K3s

Next step → introduce VLANs for segmentation
