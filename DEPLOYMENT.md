# Deployment Guide for Grapefruitmoon.net

This guide explains how to automatically deploy your Hugo site to your hosting provider using GitHub Actions.

## How It Works

1. You push changes to the `master` branch
2. GitHub Actions automatically:
   - Checks out your code
   - Installs Hugo
   - Builds your site
   - Deploys to your hosting server

## Setup Instructions

### Step 1: Choose Your Deployment Method

Your hosting provider likely supports one of these:

- **FTP/FTPS** - Most common for shared hosting (cPanel, Plesk, etc.)
- **SSH/SFTP** - More secure, available on VPS or better hosting plans

### Step 2: Add Secrets to GitHub

GitHub Secrets keep your credentials secure.

1. Go to your GitHub repository: https://github.com/alanjwood/grapefruitmoonnet
2. Click **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**

#### For FTP Deployment:

Add these secrets:

- `FTP_SERVER` - Your FTP server (e.g., `ftp.yourhost.com`)
- `FTP_USERNAME` - Your FTP username
- `FTP_PASSWORD` - Your FTP password

#### For SSH Deployment:

Add these secrets:

- `SSH_PRIVATE_KEY` - Your SSH private key (see below)
- `REMOTE_HOST` - Your server IP or hostname (e.g., `yoursite.com`)
- `REMOTE_USER` - SSH username (e.g., `username`)
- `REMOTE_TARGET` - Target directory on server (e.g., `/home/username/public_html/`)

##### Generating SSH Key:

```bash
# On your local machine:
ssh-keygen -t ed25519 -C "github-actions"

# Copy the PRIVATE key (id_ed25519):
cat ~/.ssh/id_ed25519

# Copy the PUBLIC key to your server:
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@yourserver.com
```

### Step 3: Enable Your Deployment Method

Edit `.github/workflows/deploy.yml`:

**For FTP:**
```yaml
- name: Deploy via FTP
  uses: SamKirkland/FTP-Deploy-Action@v4.3.5
  if: true  # Changed from 'false' to 'true'
  with:
    server: ${{ secrets.FTP_SERVER }}
    username: ${{ secrets.FTP_USERNAME }}
    password: ${{ secrets.FTP_PASSWORD }}
    local-dir: ./public/
    server-dir: /public_html/  # Change this to your path
```

**For SSH:**
```yaml
- name: Deploy via SSH
  uses: easingthemes/ssh-deploy@v5.1.0
  if: true  # Changed from 'false' to 'true'
  with:
    SSH_PRIVATE_KEY: ${{ secrets.SSH_PRIVATE_KEY }}
    REMOTE_HOST: ${{ secrets.REMOTE_HOST }}
    REMOTE_USER: ${{ secrets.REMOTE_USER }}
    SOURCE: "public/"
    TARGET: ${{ secrets.REMOTE_TARGET }}
    ARGS: "-avz --delete"
```

### Step 4: Adjust Paths

**Important:** Update `server-dir` (FTP) or `TARGET` (SSH) to match your hosting:

Common paths:
- cPanel: `/public_html/` or `/home/username/public_html/`
- Plesk: `/httpdocs/`
- Custom VPS: `/var/www/html/` or wherever you configured

### Step 5: Test Deployment

1. Commit and push your changes:
   ```bash
   git add .github/workflows/deploy.yml
   git commit -m "Add GitHub Actions deployment"
   git push origin master
   ```

2. Check the Actions tab on GitHub:
   - https://github.com/alanjwood/grapefruitmoonnet/actions
   - You'll see your deployment running
   - Green checkmark = success!
   - Red X = check the logs for errors

## Common Hosting Providers

### cPanel (Most shared hosts)
- Method: FTP or SSH
- Server: Usually `ftp.yourdomain.com` or `yourdomain.com`
- Directory: `/public_html/` or `/home/username/public_html/`

### Cloudways / DigitalOcean / Vultr
- Method: SSH (preferred)
- Get SSH credentials from your hosting panel

### Netlify / Vercel / Cloudflare Pages
- Don't need GitHub Actions - they have built-in Hugo support
- Just connect your GitHub repo in their dashboard

### AWS S3 / DigitalOcean Spaces
- Need different workflow (can provide if needed)

## Troubleshooting

### Deployment fails with "Permission denied"
- FTP: Check username/password
- SSH: Ensure public key is added to server's `~/.ssh/authorized_keys`

### Files aren't showing up
- Check `server-dir` or `TARGET` path
- Verify directory permissions on server (usually 755)

### Old files remain after deployment
- SSH: The `--delete` flag should remove them
- FTP: May need to manually clear old files first

## Manual Deployment (Backup Method)

If GitHub Actions isn't working, you can deploy manually:

```bash
# Build the site locally
hugo

# Upload the public/ folder via FTP client or rsync:
rsync -avz --delete public/ user@server:/path/to/public_html/
```

## Need Help?

Check common issues:
1. Verify secrets are correctly set in GitHub
2. Check the Actions tab for error logs
3. Test FTP/SSH credentials manually first
4. Ensure Hugo builds successfully locally (`hugo`)

## Current Status

- ✅ Workflow file created (`.github/workflows/deploy.yml`)
- ⏳ Waiting for secrets to be configured
- ⏳ Deployment method to be enabled (change `if: false` to `if: true`)
