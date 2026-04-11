# 🚀 Azure VM Website Deployment Guide (Tooplate + Domain + HTTPS)

## 📌 Overview
This guide documents the complete process of:
- Creating an Azure VM
- Connecting via SSH
- Deploying a Tooplate website
- Connecting a domain (Name.com)
- Enabling HTTPS with Certbot

---

# 🧱 1. Create Azure Virtual Machine

## Basic Configuration
- OS: Ubuntu 24.04 LTS
- Username: azureuser
- Authentication: SSH key
- SSH Key: Generated .pem file
- Open ports:
  - SSH (22)

---

# 🔑 2. SSH into VM

chmod 400 ~/Downloads/tooplateKey.pem
ssh -i ~/Downloads/tooplateKey.pem azureuser@<PUBLIC_IP>

---

# ⚙️ 3. Server Setup

sudo apt update && sudo apt upgrade -y
sudo apt install nginx unzip wget -y
sudo systemctl start nginx
sudo systemctl enable nginx

---

# 🌐 4. Deploy Tooplate Website

cd /tmp
wget "TOOPLATE_ZIP_URL" -O site.zip
unzip site.zip
sudo rm -rf /var/www/html/*
sudo cp -r TEMPLATE_FOLDER/* /var/www/html/
sudo chmod -R 755 /var/www/html
sudo systemctl restart nginx

---

# 🌍 5. Domain Setup (Name.com)

A Record:
Type: A
Host: @
Value: <PUBLIC_IP>

WWW Record:
Type: A
Host: www
Value: <PUBLIC_IP>

---

# 🔓 6. Azure Networking Rules

Open ports:
- 22 (SSH)
- 80 (HTTP)
- 443 (HTTPS)

All must be set to Source: Any

---

# ⚙️ 7. Nginx Domain Configuration

sudo nano /etc/nginx/sites-available/vishdevops.systems

```
server {
    listen 80;
    server_name vishdevops.systems www.vishdevops.systems;

    root /var/www/html;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
```bash
sudo ln -s /etc/nginx/sites-available/vishdevops.systems /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/default
sudo nginx -t
sudo systemctl restart nginx
```

---

# 🔒 8. Enable HTTPS (Certbot)

sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d vishdevops.systems -d www.vishdevops.systems

---

# ✅ Final Result

https://vishdevops.systems
