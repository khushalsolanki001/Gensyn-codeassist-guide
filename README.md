# 💻 Gensyn-codeassist-

## 🚀 TUTORIAL: Running CodeAssist on a VPS

This guide provides a robust and secure method for running the CodeAssist web interface on a Virtual Private Server (VPS). We utilize port `3001` to prevent common conflicts (e.g., with Swarm services on port `3000`) and access the UI via an SSH tunnel.

> **Recommended VPS Specifications:** **4 CPU**, **8 GB RAM**, **32 GB Swap** (Consult your VPS provider's documentation on configuring swap memory).

---

### 1. ⚙️ Setup on the VPS (Remote Server Steps)

Perform these steps after logging into your VPS via SSH.

#### 1.1. Create a Screen Session
A `screen` session prevents the process from terminating if your SSH connection is unexpectedly dropped.

```bash
screen -S codeassist
1.2. Install UV (Python Packager)
Install the highly efficient UV package manager.

Bash

curl -LsSf [https://astral.sh/uv/install.sh](https://astral.sh/uv/install.sh) | sh
1.3. Reload Bash Configuration
Load the new uv path into your current shell session.

Bash

source ~/.bashrc
1.4. Clone the Repository
Clone the CodeAssist repository and navigate into the directory.

Bash

git clone [https://github.com/gensyn-ai/codeassist.git](https://github.com/gensyn-ai/codeassist.git)
cd codeassist
1.5. Configure Port in compose.yml
Edit the Docker Compose file to map the host port to 3001.

Bash

nano compose.yml
Find the line containing 3000:3000 and change the host port (the first number) to 3001:

YAML

      - 3001:3000  # <--- Change this line
Press Ctrl + X, then Y, then Enter to save and exit nano.

1.6. Run the Program
Start the CodeAssist application. The --port 3001 ensures the application binds correctly within the Docker container.

Bash

uv run run.py --port 3001
Hugging Face Token: When prompted, paste your Hugging Face token and press Enter. Note: The input will be invisible for security; this is normal.

2. 🔐 Connect via SSH Tunnel (Local PC Steps)
This step is critical. The web interface is only accessible through a secure SSH tunnel from your local computer.

Standard Connection (Password/Default Key)
Open a new terminal (CMD/PowerShell/Terminal) on your local computer.

Bash

# General SSH Tunnel Command
ssh -L 3001:localhost:3001 -L 8000:localhost:8000 -L 8008:localhost:8008 root@[YOUR_VPS_IP]
Key-Based Connection (AWS/GCP/Azure - using .pem or .ppk)
If your VPS uses a key file (.pem or similar) for authentication (common with cloud providers like Google Cloud or AWS), you must specify the key file path using the -i flag.

Example for Windows/Linux/macOS:

Bash

# Key-Based SSH Tunnel Command
ssh -i "[PATH/TO/YOUR.pem]" -L 3001:localhost:3001 -L 8000:localhost:8000 -L 8008:localhost:8008 [YOUR_SSH_USER]@[YOUR_VPS_IP]
Example of a key file path: C:/Users/khush/.ssh/khushal1.pem

Replace [YOUR_VPS_IP] with your VPS's IP address.

Replace [YOUR_SSH_USER] with your VPS username (e.g., ec2-user, ubuntu, khushal1).

Leave the local terminal window open and running for the entire session.

3. 🌐 Access the Interface
3.1. Open the Web Interface
On your local computer, open a web browser and navigate to:

http://localhost:3001
You are now securely connected to the CodeAssist web interface running on your VPS.

4. ✅ Finish & Training
4.1. Initiate Training
After you have completed at least one code task in the web interface:

Return to your VPS terminal (the one inside the screen -S codeassist session).

Press Ctrl + C to stop the uv run run.py process.

The training process will automatically begin. Allow this process time to complete.

4.2. Verification
Check your Hugging Face account to confirm that the new model has been successfully uploaded. If the new model is visible, the entire process is complete.

🛠️ One-Click Copy Section
For quick setup, copy and paste the commands below into your VPS terminal.

1. VPS Setup Commands
Bash

# Create screen session
screen -S codeassist
# Install UV
curl -LsSf [https://astral.sh/uv/install.sh](https://astral.sh/uv/install.sh) | sh
# Reload Bash
source ~/.bashrc
# Clone repo and navigate
git clone [https://github.com/gensyn-ai/codeassist.git](https://github.com/gensyn-ai/codeassist.git)
cd codeassist
# <<< IMPORTANT: Manually edit compose.yml here! >>>
# Change 3000:3000 to 3001:3000 using nano compose.yml
# Run program (Paste Hugging Face token when prompted)
uv run run.py --port 3001
2. Local PC Tunnel Commands
A. Standard Connection:

Bash

ssh -L 3001:localhost:3001 -L 8000:localhost:8000 -L 8008:localhost:8008 root@[YOUR_VPS_IP]
B. Key-Based Connection (e.g., AWS/GCP):

Bash

ssh -i "[PATH/TO/YOUR.pem]" -L 3001:localhost:3001 -L 8000:localhost:8000 -L 8008:localhost:8008 [YOUR_SSH_USER]@[YOUR_VPS_IP]
C. Web Interface URL:

http://localhost:3001
