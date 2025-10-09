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

