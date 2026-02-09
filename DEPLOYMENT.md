# CI/CD: Deploy to a virtual server via GitHub Actions

Pushing to the `main` branch triggers a build and deploy to your server over SSH. This guide explains how to set it up once.

## Overview

1. **GitHub Actions** (`.github/workflows/docker-image.yml`) runs on every push to `main`.
2. It builds the backend JAR and frontend, then packs a tarball with Dockerfiles and config.
3. The tarball is copied to your server via **SCP**; then over **SSH** the workflow runs `docker compose -f docker-compose.prod.yaml build` and `up -d`.
4. Production env vars are taken from a **GitHub Secret** (no `.env` file is stored in the repo).

## 1. Prepare the virtual server

Your server needs to have **Docker** installed. You need:

- **Docker Compose** (v2) so the workflow can run `docker compose` on the server. Check with: `docker compose version`.
- The user that GitHub uses for SSH (see below) must be able to run Docker without `sudo` (e.g. in the `docker` group: `sudo usermod -aG docker USERNAME`).

The deployment runs **nginx in a container** (via `docker-compose.prod.yaml`), so your host nginx does not need to be configured for this app—the compose file starts its own nginx container. If you prefer to use the host nginx as a reverse proxy in front of the stack, you can do that separately; the default setup is nginx inside Docker.

### SSH access for GitHub Actions

GitHub will log in with an SSH key. If you use a firewall, allow SSH (port 22).

## 2. Add a key pair for the deploy user (step-by-step)

Do this on your **local machine** (or any machine where you can later paste the private key into GitHub).

### Step 1: Generate the key pair

In a terminal (PowerShell, bash, etc.):

```bash
ssh-keygen -t ed25519 -C "github-actions-borsibaar" -f deploy_key
```

When it asks for a passphrase, **press Enter** for no passphrase (GitHub Actions cannot use a passphrase).

- **`-t ed25519`** – use Ed25519 keys (recommended).
- **`-f deploy_key`** – creates `deploy_key` (private) and `deploy_key.pub` (public).

You should see two files: `deploy_key` and `deploy_key.pub`. Do **not** commit them. If they’re inside your repo folder, add to `.gitignore`: `deploy_key` and `deploy_key.pub`.

### Step 2: Put the public key on the server

The **deploy** user on the server must accept logins with this key.

1. **Open the public key file** (`deploy_key.pub`). It’s one line, e.g.  
   `ssh-ed25519 AAAAC3NzaC1lZDI1NTE5... github-actions-borsibaar`

2. **On the server**, as an admin user:
   ```bash
   sudo su - deploy
   mkdir -p ~/.ssh
   chmod 700 ~/.ssh
   nano ~/.ssh/authorized_keys
   ```
   Paste the **entire line** from `deploy_key.pub` into that file, save and exit.

3. **Set permissions:**
   ```bash
   chmod 600 ~/.ssh/authorized_keys
   exit
   ```

4. **(Recommended)** Add the deploy user to the `docker` group so it can run Docker:
   ```bash
   sudo usermod -aG docker deploy
   ```
   The user may need to log out and back in for the group to apply; for GitHub Actions it will apply on next login.

### Step 3: Test login (optional)

From your local machine:

```bash
ssh -i deploy_key deploy@YOUR_SERVER_IP
```

If you get a shell as `deploy`, the key is set up correctly. Type `exit` to close.

### Step 4: Put the private key in GitHub

1. Open the **private** key file (`deploy_key`) in a text editor.
2. Copy the **entire** contents, including the lines:
   - `-----BEGIN OPENSSH PRIVATE KEY-----`
   - `-----END OPENSSH PRIVATE KEY-----`
3. In GitHub: repo **Settings → Secrets and variables → Actions**.
4. Click **New repository secret**.
5. Name: `SSH_PRIVATE_KEY`. Value: paste the full private key. Save.

You can delete the key files from your local machine after the secret is saved (or keep them in a safe place and never commit them).

## 3. Add GitHub Secrets

In your GitHub repo: **Settings → Secrets and variables → Actions → New repository secret.**

Create these **Secrets** (not Variables):

| Secret name             | Value |
|-------------------------|--------|
| `SSH_USER`              | User that runs deploy on the server (e.g. `deploy` or `ubuntu`). |
| `SSH_PRIVATE_KEY`       | **Full** contents of the **private** key file (e.g. `deploy_key`). Include the lines `-----BEGIN ... KEY-----` and `-----END ... KEY-----`. |
| `SERVER_IP`             | Hostname or IP of the server (e.g. `borsibaar.zapto.org` or `1.2.3.4`). |
| `ENV_PRODUCTION_FILE`   | **Full** contents of your production `.env` file (see below). |

### What to put in `ENV_PRODUCTION_FILE`

Use the same variables you would put in `.env` on the server. The workflow writes this to `.env.production` and the server copies it to `.env` for Docker Compose. Include at least:

- `POSTGRES_DB`, `POSTGRES_USER`, `POSTGRES_PASSWORD`
- `SPRING_DATASOURCE_URL=jdbc:postgresql://postgres:5432/YOUR_DB_NAME`, `SPRING_DATASOURCE_USERNAME`, `SPRING_DATASOURCE_PASSWORD`
- `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`
- `JWT_SECRET` (e.g. `openssl rand -base64 32`)
- `APP_CORS_ALLOWED_ORIGINS` (e.g. `https://your-domain.com`)
- `APP_FRONTEND_URL` (e.g. `https://your-domain.com`)
- `NEXT_PUBLIC_BACKEND_URL` (e.g. `https://your-domain.com` – used for OAuth redirects)

Paste the **entire** file content into the secret (multi-line is fine). Do not commit this file to the repo.

## 4. Add HTTPS with Certbot




Once these steps are done, every push to `main` will build and deploy using Docker on your virtual server.
