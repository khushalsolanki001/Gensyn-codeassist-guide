# 💻 Gensyn-CodeAssist: VPS Deployment and Training Guide

This document outlines the professional deployment and operational procedure for running the Gensyn CodeAssist web interface on a Virtual Private Server (VPS). Access is secured via an SSH port-forwarding tunnel on host port `3001`.

> **System Prerequisites:**
> * **Operating System:** Ubuntu 20.04+ or Debian equivalent.
> * **Minimum Specifications:** **4 CPU**, **8 GB RAM**, **32 GB Swap** (Crucial for model training).

---

### 1. 🛠️ VPS Preparation: Dependencies and Setup

This initial phase ensures all required software (Docker and package manager) is correctly installed.

#### 1.1. Install Docker and Docker Compose Plugins

Execute the following commands on your VPS to install the necessary Docker components.

**➡️ Copy and paste the entire block below into your VPS terminal:**

```bash
# 1. Update system packages
sudo apt update && sudo apt upgrade -y

# 2. Install prerequisites for Docker installation
sudo apt install -y ca-certificates curl gnupg lsb-release

# 3. Add Docker's official GPG key and repository
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL [https://download.docker.com/linux/ubuntu/gpg](https://download.docker.com/linux/ubuntu/gpg) | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=\"$(dpkg --print-architecture)\" signed-by=/etc/apt/keyrings/docker.gpg] [https://download.docker.com/linux/ubuntu](https://download.docker.com/linux/ubuntu) \
  \"$(. /etc/os-release && echo "$VERSION_CODENAME")\" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 4. Install Docker Engine and Plugins
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 5. Add current user to the docker group for non-root execution
sudo usermod -aG docker "$USER"

echo "--- Docker installation complete. It is highly recommended to close your SSH session and reconnect to apply the user group changes! ---"

1.2. Install UV and Clone Repository
These commands set up the project environment.

➡️ Copy and paste the entire block below into your VPS terminal:

# 1. Start a persistent screen session
screen -S codeassist

# 2. Install UV (Python Packager)
curl -LsSf [https://astral.sh/uv/install.sh](https://astral.sh/uv/install.sh) | sh

# 3. Reload Bash configuration to recognize 'uv'
source ~/.bashrc

# 4. Clone the CodeAssist repository
git clone [https://github.com/gensyn-ai/codeassist.git](https://github.com/gensyn-ai/codeassist.git)
cd codeassist

# 5. Modify compose.yml for host port 3001 (Avoids conflicts with port 3000)
# Uses sed for non-interactive editing: 3000:3000 -> 3001:3000
sed -i 's/3000:3000/3001:3000/g' compose.yml

# 6. Execute the application
# NOTE: The application will prompt for your Hugging Face Access Token. 
# Input will be hidden for security (invisible typing).
uv run run.py --port 3001

2. 🔐 Secure Local Access via SSH Tunnel
The CodeAssist interface is designed to be accessed only via a secure local port-forwarding connection. This step must be performed on your LOCAL MACHINE.

Requirement: This terminal must remain open and connected for the duration of your session.

2.1. Tunnel using Password Authentication (or default SSH key)
➡️ Copy and paste the block below into your Local Terminal:

# Replace [YOUR_VPS_IP] with the server's public IP address.
ssh -L 3001:localhost:3001 -L 8000:localhost:8000 -L 8008:localhost:8008 root@[YOUR_VPS_IP]

2.2. Tunnel using Key-Based Authentication (.pem file - common for Cloud VPS)
If your VPS uses a private key (.pem, .ppk), you must explicitly specify its path.

➡️ Copy and paste the block below into your Local Terminal:

Bash

# 1. Replace [PATH/TO/YOUR.pem] (e.g., C:/Users/user/.ssh/key.pem)
# 2. Replace [YOUR_SSH_USER] (e.g., ec2-user, ubuntu, yourname)
# 3. Replace [YOUR_VPS_IP]
ssh -i "[PATH/TO/YOUR.pem]" -L 3001:localhost:3001 -L 8000:localhost:8000 -L 8008:localhost:8008 [YOUR_SSH_USER]@[YOUR_VPS_IP]
3. ✅ Operation and Training Cycle
3.1. Access the Web Interface
Once the local tunnel is established, access the interface securely on your local browser:

➡️ Access URL:

http://localhost:3001
3.2. Initiate Training and Verification
After you have completed one or more assigned code tasks in the web interface:

Return to your VPS terminal (the one running CodeAssist inside the screen session).

Press Ctrl + C to terminate the uv run run.py process.

The model training and upload process will commence automatically.

Verification: Confirm the operation is successful by checking your Hugging Face account for the newly uploaded model artifact.
