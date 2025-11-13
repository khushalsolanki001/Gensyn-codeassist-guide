# Gensyn-CodeAssist — GitHub-ready deployment docs

This repository content bundle contains two ready-to-commit files you can add to your GitHub repo:

1. `README.md` — a cleaned, copy-paste friendly deployment guide for Gensyn CodeAssist.
2. `docs/copyable-guide.html` — a static HTML version of the same guide with reliable "Copy block" and "Copy entire guide" buttons. This file is intended to be served with GitHub Pages (or any static hosting) because GitHub's repository `README.md` pages do not execute JavaScript.

---

## Instructions

**Recommended repo structure** (example):

```
/ (repo root)
├── README.md
└── docs/
    └── copyable-guide.html
```

**How to enable interactive copy buttons on GitHub:**

* Push both files to your repo as shown above.
* In the repository settings, enable **GitHub Pages** and set the source to the `docs/` folder on the `main` branch (or whichever branch you're using).
* The copyable guide will then be available at `https://<your-username>.github.io/<repo-name>/copyable-guide.html` and the copy buttons will work because GitHub Pages serves the HTML with JavaScript enabled.

**Note:** `README.md` is provided for repository browsing and for users who prefer a plain-markdown reference. `README.md` will show on GitHub but JavaScript will not run there; interactive copy buttons require GitHub Pages (or other static hosting).

---

## File 1 — README.md (add to repo root)

> Add this exact file as `README.md` at the repository root. It is a cleaned, copy-friendly Markdown version of your deployment guide (no inline markdown links inside code blocks).

<!-- README.md content starts here -->

# 💻 Gensyn-CodeAssist: VPS Deployment and Training Guide

**Access:** web UI over an SSH port-forwarded tunnel on local host port `3001`.

**System prerequisites**

* **OS:** Ubuntu 20.04+ or Debian equivalent
* **Minimum specs:** 4 CPU, 8 GB RAM, 32 GB swap (important for model training)

---

## 1. 🛠️ VPS Preparation: Dependencies and setup

This phase installs required software, including Docker and the `uv` packager.

### 1.1 Install Docker and system dependencies

Copy & paste the entire block below into your **VPS terminal**:

```bash
# 1. Update system packages
sudo apt update && sudo apt upgrade -y

# 2. Install prerequisites for Docker
sudo apt install -y ca-certificates curl gnupg lsb-release

# 3. Add Docker's official GPG key and repository
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=\"$(dpkg --print-architecture)\" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  \"$(. /etc/os-release && echo "$VERSION_CODENAME")\" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 4. Install Docker Engine and plugins
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 5. Add current user to docker group for non-root Docker usage
sudo usermod -aG docker "$USER"

echo "--- Docker installation complete. Close and reopen your SSH session (or log out and log back in) to apply group changes. ---"
```

> **Note:** After `usermod -aG docker`, the new group membership takes effect only after you reconnect. Reconnect your SSH session before running Docker commands without `sudo`.

---

### 1.2 Install `uv`, clone repository, and configure ports

Run these on the VPS (start a persistent session like `screen` or `tmux` if you want the process to survive disconnects):

```bash
# Start a screen session (optional but recommended)
screen -S codeassist

# Install UV (Python packager)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Reload your shell so 'uv' is available (or open a new session)
source ~/.bashrc

# Clone the CodeAssist repo
git clone https://github.com/gensyn-ai/codeassist.git
cd codeassist
```

#### Manual Configuration Step 1 — edit `compose.yml`

Open `compose.yml` and map host port `3001` to container port `3000`:

```yaml
ports:
  - "3001:3000"  # host:container
```

Save and exit.

#### Manual Configuration Step 2 — (optional) check `run.py`

Most setups only need the `compose.yml` change. If `run.py` contains a hardcoded host port, update it to match (usually you only need `uv run run.py --port 3001` below). If you decide to edit `run.py`, open it, find any explicit port numbers for the web UI and make them consistent with `3001` for host usage — but do **not** change container internals unless you know what you're doing.

---

### 1.3 Start the application

Run the application on the VPS (it will ask for your Hugging Face access token — input is hidden):

```bash
uv run run.py --port 3001
```

> If you're using Docker Compose instead: `sudo docker compose up -d` (after making sure `compose.yml` is configured correctly).

---

## 2. 🔐 Secure local access via SSH tunnel (run on your local machine)

**Important:** open a new local terminal window and keep it open while using the web UI.

### 2.1 Using password (or default SSH key)

Replace `[YOUR_VPS_IP]` with the server's public IP:

```bash
ssh -L 3001:localhost:3001 -L 8000:localhost:8000 -L 8008:localhost:8008 root@[YOUR_VPS_IP]
```

### 2.2 Using private key (e.g., `.pem` on cloud hosts)

Replace placeholders as described:

```bash
ssh -i "/path/to/your.pem" -L 3001:localhost:3001 -L 8000:localhost:8000 -L 8008:localhost:8008 [YOUR_SSH_USER]@[YOUR_VPS_IP]
```

---

## 3. ✅ Operation and training cycle

### 3.1 Access the web interface

On your local machine, point a browser to:

```
http://localhost:3001
```

### 3.2 Initiate training and verification

1. Complete one or more assigned code tasks in the web interface.
2. Return to your VPS terminal (the one inside `screen` if used).
3. Press `Ctrl+C` to terminate `uv run run.py`. The model training + upload process should begin automatically (per your app behavior).
4. Verify the uploaded model artifact in your Hugging Face account.

---

## Notes & fixes made

* Removed inline markdown link syntax **inside code blocks** (that breaks copy/paste in many terminals).
* Clarified port mapping `3001:3000` and recommended reconnect after adding user to `docker` group.
* Marked `run.py` changes optional — usually `compose.yml` is sufficient.

<!-- README.md content ends here -->

---

## File 2 — docs/copyable-guide.html (add to `docs/` folder and enable GitHub Pages)

> Save this file as `docs/copyable-guide.html`. Serve it with GitHub Pages (or any static host) to get working Copy buttons.

<!-- HTML file starts here -->

<!doctype html>

<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Gensyn-CodeAssist — Deployment Guide (Copyable)</title>
  <style>
    body { font-family: system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial; padding: 24px; max-width: 980px; }
    pre { background:#0f172a; color: #e6eef8; padding: 12px; overflow:auto; border-radius:6px; }
    .code-block { margin-bottom: 12px; }
    button { margin-top: 6px; padding:6px 10px; border-radius:6px; border:1px solid #ccc; cursor:pointer; }
  </style>
</head>
<body>
  <h1>Gensyn-CodeAssist — Deployment Guide</h1>
  <p>Interactive copy buttons work when this file is served by GitHub Pages or other static hosting.</p>

  <div id="deploy-guide">
    <h2>1. Install Docker and system dependencies</h2>
    <div class="code-block">
      <pre><code id="code-block-1"># 1. Update system packages
sudo apt update && sudo apt upgrade -y

# 2. Install prerequisites for Docker

sudo apt install -y ca-certificates curl gnupg lsb-release

# 3. Add Docker's official GPG key and repository

sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL [https://download.docker.com/linux/ubuntu/gpg](https://download.docker.com/linux/ubuntu/gpg) | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo 
"deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] [https://download.docker.com/linux/ubuntu](https://download.docker.com/linux/ubuntu) 
"$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | 
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 4. Install Docker Engine and plugins

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 5. Add current user to docker group for non-root Docker usage

sudo usermod -aG docker "$USER"

echo "--- Docker installation complete. Close and reopen your SSH session (or log out and log back in) to apply group changes. ---"</code></pre> <button class="copy-btn" data-target="code-block-1">Copy block</button> </div>

```
<h2>2. Install uv, clone repo, configure ports</h2>
<div class="code-block">
  <pre><code id="code-block-2"># Start a screen session (optional but recommended)
```

screen -S codeassist

# Install UV (Python packager)

curl -LsSf [https://astral.sh/uv/install.sh](https://astral.sh/uv/install.sh) | sh

# Reload your shell so 'uv' is available (or open a new session)

source ~/.bashrc

# Clone the CodeAssist repo

git clone [https://github.com/gensyn-ai/codeassist.git](https://github.com/gensyn-ai/codeassist.git)
cd codeassist</code></pre> <button class="copy-btn" data-target="code-block-2">Copy block</button> </div>

```
<h2>3. Start the application</h2>
<div class="code-block">
  <pre><code id="code-block-3">uv run run.py --port 3001
```

# or, if using Docker Compose:

sudo docker compose up -d</code></pre> <button class="copy-btn" data-target="code-block-3">Copy block</button> </div>

```
<p><button id="copy-entire" data-target-container="deploy-guide">Copy entire guide</button></p>
```

  </div>

  <script>
    async function copyText(text) {
      try {
        await navigator.clipboard.writeText(text);
        return true;
      } catch (err) {
        const ta = document.createElement('textarea');
        ta.value = text;
        ta.style.position = 'fixed';
        ta.style.left = '-9999px';
        document.body.appendChild(ta);
        ta.focus();
        ta.select();
        try {
          document.execCommand('copy');
          document.body.removeChild(ta);
          return true;
        } catch (e) {
          document.body.removeChild(ta);
          return false;
        }
      }
    }

    document.querySelectorAll('.copy-btn').forEach(btn => {
      btn.addEventListener('click', async () => {
        const targetId = btn.getAttribute('data-target');
        const el = document.getElementById(targetId);
        if (!el) return alert('Target not found');
        const text = el.innerText;
        const ok = await copyText(text);
        btn.textContent = ok ? 'Copied!' : 'Copy failed';
        setTimeout(() => btn.textContent = 'Copy block', 1500);
      });
    });

    document.getElementById('copy-entire').addEventListener('click', async (e) => {
      const containerId = e.currentTarget.getAttribute('data-target-container');
      const container = document.getElementById(containerId);
      if (!container) return alert('Container not found');

      const codeElements = container.querySelectorAll('pre > code, code');
      let combined = '';
      if (codeElements.length > 0) {
        codeElements.forEach((c, idx) => {
          combined += c.innerText.trim();
          if (idx < codeElements.length - 1) combined += '\n\n';
        });
      } else {
        combined = container.innerText;
      }

      const ok = await copyText(combined);
      e.currentTarget.textContent = ok ? 'Copied!' : 'Copy failed';
      setTimeout(() => e.currentTarget.textContent = 'Copy entire guide', 1500);
    });
  </script>

</body>
</html>

<!-- HTML file ends here -->

---

## Final notes

* If you want, I can produce a `git` patch or a zipped archive with these two files ready to commit.
* If you prefer a different styling or additional sections (e.g., troubleshooting, service unit files, or `docker-compose` full example), tell me what to include and I will add them.
