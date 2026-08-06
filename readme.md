# Ansible Automation: UFW & System Updates

A lightweight Ansible project for managing firewall rules (UFW) and system updates across Debian based distros.

## 📂 Project Structure

```bash.
├── ansible.cfg            # Configuration (points to ./inventory/)
├── inventory/
│   └── hosts              # Inventory file
├── playbooks/
│   ├── apt.yml            # System updates (safe upgrade, no kernel)
│   └── ufw.yml            # Firewall setup (SSH + Cockpit)
└── README.md
```
## 🚀 Quick Start

### 1. Prerequisites
- **Control Node**: Any Linux/macOS system with Ansible installed (WSL or Linux VM in Windows).
- **Managed Nodes**: Target servers with SSH enabled.
- **Collections**: `community.general` (required for UFW).

```bash
# Install Ansible (Example for Debian/Ubuntu)
sudo nala install ansible

# Install Required Collection
ansible-galaxy collection install community.general   
```

### 2. SSH Key Setup (One-Time)
Generate a key on the control node and copy it to your targets to enable passwordless login.

```bash
# Generate Key
ssh-keygen -t ed25519 -f ~/.ssh/id_rsa

# Copy to Target Server (replace IP/user)
ssh-copy-id -i ~/.ssh/id_rsa.pub user@192.168.1.100   
```

### 3. Configure Inventory
Edit inventory/hosts to match your network IPs and users:

```bash
[debian_hosts]
localhost ansible_connection=local
debian-vm ansible_host=127.0.0.1 ansible_port=1022 ansible_user=tom
linux-mint ansible_host=192.168.0.47 ansible_user=cap

[firewall_hosts]
localhost
debian-vm
```

## ▶️ Usage

### Update Systems (apt.yml)
Performs a safe upgrade (apt-get upgrade) skipping kernel updates.
```bash
ansible-playbook playbooks/apt.yml -K -v
```
- **K**: Prompts for sudo password on remote hosts.

### Configure Firewall (ufw.yml)

Installs UFW, allows SSH (22) & Cockpit (9090), blocks others.
```bash
ansible-playbook playbooks/ufw.yml -K -v
```

### Verification
Check connectivity before running playbooks:
```bash
ansible all -m ping
```

## 🔒 Security Notes

- **Private Keys**: Stored in ~/.ssh/ on the control node. Ensure permissions are 600.
- **Sudo: Playbooks require become**: true. Use -K to enter the password securely at runtime.
- **Idempotency**: Safe to run multiple times; only changes are applied.

## 🛠 Troubleshooting

- **Permission Denied**: Ensure SSH keys are copied correctly (ssh-copy-id).
- **Connection Refused**: Check SSH service and firewall rules on target hosts.
- **Module Not Found**: Run ansible-galaxy collection install community.general.
