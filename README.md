# Gensyn-codeassist-guide
Gensyn-codeassist-
🚀 TUTORIAL: Running CodeAssist on a VPS
This tutorial details the steps to deploy and run the CodeAssist web interface on a Virtual Private Server (VPS) and access it via a secure SSH tunnel. Port 3001 is used to prevent conflicts, especially with systems running Swarm or other services on port 3000.

Recommended VPS Specifications: 4 CPU, 8 GB RAM, 32 GB Swap (Consult your VPS provider or guides on how to configure swap memory).

1. Setup on the VPS
These steps are performed on your VPS via SSH.

1.1. Create a Screen Session
A screen session ensures the process continues running even if your SSH connection is lost.

Bash

screen -S codeassist
1.2. Install UV (Python Packager)
Install the UV package manager and pipe it directly to sh for execution.

Bash

curl -LsSf https://astral.sh/uv/install.sh | sh
1.3. Reload Bash Configuration
This makes the uv command available immediately in the current shell session.

Bash

source ~/.bashrc
1.4. Clone the Repository
Clone the CodeAssist repository and navigate into the directory.

Bash

git clone https://github.com/gensyn-ai/codeassist.git
cd codeassist
1.5. Configure Port in compose.yml
Edit the Docker Compose file to change the host port exposed for the web interface from the default 3000 to 3001.

Bash

nano compose.yml
In the file, find the line:

YAML

      - 3000:3000
And change the host port (the first 3000) to 3001:

YAML

      - 3001:3000
Press Ctrl + X, then Y, then Enter to save and exit nano.

1.6. Run the Program
Start the CodeAssist application using uv.

Bash

uv run run.py
When prompted, paste your Hugging Face token and press Enter. The token input will not be visible, which is normal.

2. Connect via SSH Tunnel (Crucial Step)
The CodeAssist interface is only accessible through an SSH tunnel from your local machine.

2.1. Create the Tunnel on your Local PC
Open a new terminal (CMD/PowerShell/Terminal) on your local computer and run the following command. This forwards the required ports from the VPS to your local machine.

Bash

ssh -L 3001:localhost:3001 -L 8000:localhost:8000 -L 8008:localhost:8008 root@[YOUR_VPS_IP]
Replace [YOUR_VPS_IP] with the actual IP address of your VPS.

Type yes if prompted, then enter your VPS password.

Leave this terminal window open for the duration of your session.

2.2. Access the Web Interface
On your local PC, open a web browser and navigate to:

http://localhost:3001
You are now connected to the CodeAssist web interface running securely on your VPS.

3. Training and Completion
3.1. Finish and Initiate Training
After you complete one or more code tasks in the web interface:

Return to your VPS terminal (the one inside the screen -S codeassist session).

Press Ctrl + C to stop the uv run run.py process.

The training process will automatically begin. Please wait for this process to complete.

3.2. Verification
Check your Hugging Face account to confirm that the new model has been uploaded. If the model is present, the entire process is complete.
