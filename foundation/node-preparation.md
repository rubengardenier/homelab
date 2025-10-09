## Node Preparation — Step-by-Step Checklist
## 🖥️ 1. System Update & Base Tools

Run this on all nodes (controller, worker-1, worker-2):
```
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl wget git htop unzip net-tools
```
💡 This ensures every node has the same base utilities and latest security updates.


## 🧱 2. Install tmux
tmux lets you keep sessions running even if your SSH connection drops.
```
sudo apt install -y tmux
tmux new -s homelab
```

Detach with Ctrl + B, then D.
Reattach later with:
```
tmux attach -t homelab
```
✅ Great for K3s installs or long-running commands.


## ☸️ 3. Install k9s
k9s is a powerful terminal UI for managing Kubernetes.
Quick install (via Webi):
```
curl -sS https://webinstall.dev/k9s | bash
```
Or manually:
```
wget https://github.com/derailed/k9s/releases/latest/download/k9s_Linux_amd64.tar.gz
tar -xzf k9s_Linux_amd64.tar.gz
sudo mv k9s /usr/local/bin/
```
Later, once K3s is installed, just run:
```
k9s
```







