# Deployment to Linode (fruzzled.ie) using Github Actions

### 1. GitHub Repository Secrets:
Go to your GitHub repo → Settings → Secrets and variables → Actions, then add:

```
HOST: your-linode-ip-address
USERNAME: your-username
SSH_PRIVATE_KEY: your-private-ssh-key
PORT: 22 (or your custom SSH port)
```

```bash
# Generate a new SSH key specifically for GitHub Actions
ssh-keygen -t rsa -b 4096 -f ~/.ssh/github_actions_key -C "github-actions"

# Copy the public key to your server
ssh-copy-id -i ~/.ssh/github_actions_key.pub username@your-server-ip

# Copy the private key content to GitHub secrets
cat ~/.ssh/github_actions_key
# Copy this entire output to the SSH_PRIVATE_KEY secret
```

### 2. Ensure gunicorn can be restarted on the server without providing a password
```bash
sudo visudo
```
Add this line to the file : 
```bash
{USERNAME} ALL=(ALL) NOPASSWD: /usr/bin/systemctl restart gunicorn
``` 

