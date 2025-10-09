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

Each Ubuntu node uses a **manually configured static IP** via Netplan (`/etc/netplan/50-cloud-init.yaml`).  
Hostnames follow a consistent pattern for clarity inside the cluster.

| Role | Hostname | IP Address | Notes |
|------|-----------|-------------|--------|
| Controller | `controller` | `192.168.68.152` | Main K3s control plane |
| Worker 1 | `worker-1` | `192.168.68.150` | K3s worker node |
| Worker 2 | `worker-2` | `192.168.68.151` | K3s worker node |
| NAS | `ugreen-nas` | `192.168.68.50` | Ugreen NASync DXP2800 (NFS backend) |
| Gateway | `deco-router` | `192.168.68.1` | TP-Link Deco mesh router |

---

## ⚙️ Netplan Configuration (Ubuntu 24.04 LTS)

Static IPs are configured manually on each Ubuntu node.

Edit the file:
```bash
sudo nano /etc/netplan/50-cloud-init.yaml

```
Example configuration for worker-1:
```
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
```

Apply the configuration:
```
sudo netplan apply
```

To prevent Cloud-Init from overwriting manual settings, create:
```
sudo nano /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
```

and add:
```
network: {config: disabled}
```

💡 After applying, verify the active configuration using:
```
ip a
```

## 🧱 Hostnames and Local Resolution

Each node in the cluster has a clear, descriptive hostname:

Node	Hostname
Controller	controller
Worker 1	worker-1
Worker 2	worker-2

Hostnames are set once using:
```
sudo hostnamectl set-hostname worker-1
```

and verified with:
```
hostnamectl
```

To ensure smooth internal name resolution, all nodes share the same /etc/hosts entries:
```
127.0.0.1   localhost
127.0.1.1   worker-1

192.168.68.152   controller
192.168.68.150   worker-1
192.168.68.151   worker-2
192.168.68.50    ugreen-nas
```
Now you can simply run:
```
ping controller
ssh worker-2
```


