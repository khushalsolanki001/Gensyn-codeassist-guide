# 💻 Gensyn-CodeAssist: VPS Deployment and Training Guide

This document outlines the professional deployment and operational procedure for running the Gensyn CodeAssist web interface on a Virtual Private Server (VPS). Access is secured via an SSH port-forwarding tunnel on host port `3001`.

> **System Prerequisites:**
> * **Operating System:** Ubuntu 20.04+ or Debian equivalent.
> * **Minimum Specifications:** **4 CPU**, **8 GB RAM**, **32 GB Swap** (Crucial for model training).

---

### 1. 🛠️ VPS Preparation: Dependencies and Setup

This initial phase installs all required software, including Docker and the UV package manager, on your VPS.

#### 1.1. Install Docker and System Dependencies

Execute the following block of commands on your VPS to install Docker and configure user permissions.

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

echo "--- Docker installation complete. It is highly recommended to close your SSH session and reconnect to apply user group changes! ---"
1.2. Install UV, Clone Repository, and Configure Ports
These commands set up the project environment and prepare the configuration files for port 3001.

➡️ Copy and paste the entire block below into your VPS terminal:

Bash

# 1. Start a persistent screen session
screen -S codeassist

# 2. Install UV (Python Packager)
curl -LsSf [https://astral.sh/uv/install.sh](https://astral.sh/uv/install.sh) | sh

# 3. Reload Bash configuration to recognize 'uv'
source ~/.bashrc

# 4. Clone the CodeAssist repository
git clone [https://github.com/gensyn-ai/codeassist.git](https://github.com/gensyn-ai/codeassist.git)
cd codeassist
⚠️ Manual Configuration Step 1: Edit compose.yml

Open the Docker Compose file to change the host port:

Bash

nano compose.yml
Find the ports: section and change the HOST port from 3000:3000 to 3001:3000:

YAML

    ports:
      - 3001:3000  # <-- CHANGE THIS LINE
Press Ctrl + X, then Y, then Enter to save and exit nano.

⚠️ Manual Configuration Step 2: Edit run.py (Optional, for robustness)

Open the application run file to ensure the internal service port mapping is set to 3001.

Bash

nano run.py
Search for the section exposing the web UI port (usually near the top) and change the hardcoded port from 3000 to 3001:

Python

# expose web ui port
"3000/tcp": 3001,  # <-- Change the second number (container port) if necessary, or the host port number.
                   #    A common change here is to ensure the host port is 3001 if the file uses 3000.
Based on previous drafts, ensure the line related to port exposure has a 3001 visible. Press Ctrl + X, then Y, then Enter to save and exit nano.

➡️ Execute the application:

Bash

# NOTE: The application will prompt for your Hugging Face Access Token. 
# Input will be hidden for security (invisible typing).
uv run run.py --port 3001
2. 🔐 Secure Local Access via SSH Tunnel (Local Machine)
The CodeAssist interface is only accessible via a secure local port-forwarding connection. This step must be performed on your LOCAL MACHINE in a new terminal window that must remain open.

2.1. Tunnel using Password Authentication (or default SSH key)
➡️ Copy and paste the block below into your Local Terminal:

Bash

# Replace [YOUR_VPS_IP] with the server's public IP address.
ssh -L 3001:localhost:3001 -L 8000:localhost:8000 -L 8008:localhost:8008 root@[YOUR_VPS_IP]
2.2. Tunnel using Key-Based Authentication (.pem file - Cloud VPS)
If your VPS uses a private key (e.g., AWS, GCP), use the -i flag to specify the key path.

➡️ Copy and paste the block below into your Local Terminal:

Bash

# 1. Replace [PATH/TO/YOUR.pem] (e.g., C:/Users/user/.ssh/mykey.pem)
# 2. Replace [YOUR_SSH_USER] (e.g., ec2-user, ubuntu, yourname)
# 3. Replace [YOUR_VPS_IP]
ssh -i "[PATH/TO/YOUR.pem]" -L 3001:localhost:3001 -L 8000:localhost:8000 -L 8008:localhost:8008 [YOUR_SSH_USER]@[YOUR_VPS_IP]
3. ✅ Operation and Training Cycle
3.1. Access the Web Interface
Once the local tunnel is established and the application is running on the VPS, access the interface securely:

➡️ Access URL:

http://localhost:3001
3.2. Initiate Training and Verification
After you have successfully completed one or more assigned code tasks in the web interface:

Return to your VPS terminal (the one inside the screen session).

Press Ctrl + C to terminate the uv run run.py process.

The model training and upload process will commence automatically.

Verification: Confirm the successful operation by checking your Hugging Face account for the newly uploaded model artifact.
