# 🌐 Homelab — Network Plan & Configuration

The Network Plan describes how my Homelab cluster is connected, how each node receives its IP address and hostname, and how the design will evolve over time.  
It serves both as a technical reference and as documentation of my decision-making process.
 
<br>

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

<br>

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

<br>

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

| Node | Hostname |
|------|-----------|
| Controller | controller |
| Worker 1 | worker-1 |
| Worker 2 | worker-2 |

Hostnames are set once using:
```
sudo hostnamectl set-hostname worker-1
```

and verified with:
```
hostnamectl
```

## 🧩 Configure /etc/hosts for Local Name Resolution
To ensure smooth internal name resolution, all nodes share the same /etc/hosts entries.
This allows you to use hostnames (like controller, worker-1, or worker-2) instead of IP addresses.

Follow these steps on each node (controller, worker-1, and worker-2):

1. Open the hosts file:
```
sudo nano /etc/hosts
```

2. Keep the first two default lines as they are, but replace <hostname> with the correct name for that node:
- On controller → 127.0.1.1 controller
- On worker-1 → 127.0.1.1 worker-1
- On worker-2 → 127.0.1.1 worker-2

3. Add these shared lines below:   
```
192.168.68.152   controller
192.168.68.150   worker-1
192.168.68.151   worker-2
192.168.68.50    ugreen-nas
```
4. Save and close (Ctrl + O, Enter, then Ctrl + X).

5. Test from each node:
```
ping controller
ping worker-2
ssh worker-1
```
If these commands respond successfully, hostname resolution across your cluster is working correctly.

<br>

## Example /etc/hosts Files
🖥️ Controller
```
127.0.0.1   localhost
127.0.1.1   controller

192.168.68.152   controller
192.168.68.150   worker-1
192.168.68.151   worker-2
192.168.68.50    ugreen-nas
```

⚙️ Worker-1
```
127.0.0.1   localhost
127.0.1.1   worker-1

192.168.68.152   controller
192.168.68.150   worker-1
192.168.68.151   worker-2
192.168.68.50    ugreen-nas
```

⚙️ Worker-2
```
127.0.0.1   localhost
127.0.1.1   worker-2

192.168.68.152   controller
192.168.68.150   worker-1
192.168.68.151   worker-2
192.168.68.50    ugreen-nas
```


## 💻 Local Workstation (Ubuntu Client)

To allow my personal Ubuntu laptop to connect to the cluster nodes by hostname
(rather than typing IP addresses), I also added the same entries locally on my client machine.

Edit the local hosts file:
```
sudo nano /etc/hosts
```

Add the following lines at the bottom:
```
192.168.68.152   controller
192.168.68.150   worker-1
192.168.68.151   worker-2
192.168.68.50    ugreen-nas
```

Save and exit (Ctrl + O, Enter, then Ctrl + X).

Now I can connect directly from my laptop using hostnames:
```
ssh controller
ssh worker-1
ssh worker-2
```

✅ This makes administration and testing easier while keeping all devices on consistent naming.

<br>

## 🧭 Future Improvement — Centralized DNS via Pi-hole

While the current setup relies on manual `/etc/hosts` entries for hostname resolution,  
I am working toward a cleaner, centralized DNS approach using **Pi-hole**.

Pi-hole will run inside the homelab network (most likely on the Ugreen NAS) and act as the primary DNS server for all nodes.  
This will eliminate the need to manually configure hostnames on every device and allow the cluster to resolve internal domains such as:

<br>

## 🔐 Security Notes

- SSH access allowed only within LAN (192.168.68.0/24)
- NFS exports on the NAS restricted to controller and worker IPs
- No inbound ports exposed to the internet
- Deco cloud remote management disabled
- SSH key-based authentication enforced on all nodes

