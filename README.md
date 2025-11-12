🚀 Gensyn-CodeAssist: VPS Deployment Tutorial
This tutorial guides you through setting up, running, and securely accessing CodeAssist on a Virtual Private Server (VPS). We use Docker and uv for easy deployment and access the UI via a secure SSH tunnel on port 3001.

Recommended VPS Specifications: 4 CPU, 8 GB RAM, 32 GB Swap.

1. 🐳 Install Docker on VPS (Required)
If Docker is not already installed on your Ubuntu/Debian-based VPS, use these commands.

Copy and paste the entire block below into your VPS terminal:

Bash

# Update packages and install prerequisites
sudo apt update
sudo apt install -y ca-certificates curl gnupg lsb-release

# Add Docker's official GPG key
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add Docker repository to Apt sources
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Add your user to the docker group to run commands without sudo
sudo usermod -aG docker $USER
echo "--- Docker installation complete. Please log out and log back in to apply group changes! ---"
2. ⚙️ CodeAssist Setup on VPS
After logging back into your VPS (to ensure Docker group permissions are active), execute the commands below.

Copy and paste the entire block below into your VPS terminal:

Bash

# 1. Create a Screen Session (keeps process running if SSH drops)
screen -S codeassist

# 2. Install UV (Python Packager)
curl -LsSf https://astral.sh/uv/install.sh | sh

# 3. Reload Bash Configuration
source ~/.bashrc

# 4. Clone the Repository and Navigate
git clone https://github.com/gensyn-ai/codeassist.git
cd codeassist

# 5. Configure Port in compose.yml (Change 3000:3000 to 3001:3000)
# This uses 'sed' to apply the change without opening nano
sed -i 's/3000:3000/3001:3000/g' compose.yml

# 6. Run the Program
# NOTE: Paste your Hugging Face token when prompted, then press Enter.
uv run run.py --port 3001
3. 🔐 SSH Tunnel Connection (Local PC)
IMPORTANT: Open a new terminal (CMD/PowerShell/Terminal) on your local computer. This terminal must remain open while you use CodeAssist.

Option A: Standard SSH Connection (Password or Default Key)
Copy and paste this command into your local terminal:

Bash

# Replace [YOUR_VPS_IP] with the IP address of your server
ssh -L 3001:localhost:3001 -L 8000:localhost:8000 -L 8008:localhost:8008 root@[YOUR_VPS_IP]
Option B: Key-Based SSH Connection (AWS/GCP/Azure .pem key)
Copy and paste this command into your local terminal:

Bash

# 1. Replace [PATH/TO/YOUR.pem] with the path to your key file (e.g., C:/Users/user/.ssh/key.pem)
# 2. Replace [YOUR_SSH_USER] (e.g., ec2-user, ubuntu, yourname)
# 3. Replace [YOUR_VPS_IP]
ssh -i "[PATH/TO/YOUR.pem]" -L 3001:localhost:3001 -L 8000:localhost:8000 -L 8008:localhost:8008 [YOUR_SSH_USER]@[YOUR_VPS_IP]
4. 🌐 Access & Complete Training
4.1. Access the Web Interface
Once the SSH tunnel is active, open your local browser to:

Copy and paste this URL:

http://localhost:3001
4.2. Initiate Training
After completing one or more code tasks in the web interface:

Return to your VPS terminal (the one running inside the screen session).

Press Ctrl + C to stop the uv run run.py process.

The training process will start automatically. Wait for it to finish. Check your Hugging Face account to confirm the new model has been uploaded.
