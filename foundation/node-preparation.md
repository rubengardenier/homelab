# 🧩 Node Preparation — Step-by-Step Checklist

## 🖥️ 1. System Update & Base Tools

Run this on **all nodes** (`controller`, `worker-1`, `worker-2`):

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl wget git htop unzip net-tools
```

💡 This ensures every node has the same base utilities and latest security updates.

<br>

## 🧱 2. Install tmux

`tmux` lets you keep sessions running even if your SSH connection drops.  
Install it on all nodes (controller + workers) for consistency, but you’ll mostly use it on the controller — not on your laptop.

### ⚙️ Installation

```bash
sudo apt install -y tmux
```

### 💻 Your Laptop

- You use it only to SSH into your nodes.  
- You don’t need `tmux` here — your terminal (e.g. GNOME Terminal, Terminator) already handles multiple tabs.  
- So: ❌ **no need to install `tmux` on your laptop.**

### 🖥️ Controller Node

- This is where you’ll run most commands: installing K3s, deploying manifests, monitoring, etc.  
- If your SSH session disconnects during an install, `tmux` keeps it alive.  
- So: ✅ **install + actively use `tmux` here.**

**Example workflow:**
```bash
ssh controller
tmux new -s homelab
```

If you get disconnected:
```bash
ssh controller
tmux attach -t homelab
```

### ⚙️ Worker Nodes

- You rarely spend time on these — they mostly run the K3s agent.  
- Still, installing `tmux` is good practice for occasional troubleshooting.  
- So: ✅ **install it**, ❌ **no need to use it daily.**

### ✅ Summary

| Node       | Install tmux | Use actively | Purpose                           |
|-------------|--------------|--------------|-----------------------------------|
| Laptop      | ❌ No         | —            | Not needed                        |
| Controller  | ✅ Yes        | 🟢 Yes       | Safe installs, cluster management |
| Worker-1    | ✅ Yes        | 🔴 Rarely    | Troubleshooting if needed         |
| Worker-2    | ✅ Yes        | 🔴 Rarely    | Troubleshooting if needed         |

<br>

## ☸️ 3. Install k9s

`k9s` is a powerful terminal UI for managing Kubernetes resources.

### Quick Install (via Webi)

```bash
curl -sS https://webinstall.dev/k9s | bash
```

### Manual Install (alternative)

```bash
wget https://github.com/derailed/k9s/releases/latest/download/k9s_Linux_amd64.tar.gz
tar -xzf k9s_Linux_amd64.tar.gz
sudo mv k9s /usr/local/bin/
```

Once K3s is installed, you can simply run:

```bash
k9s
```
