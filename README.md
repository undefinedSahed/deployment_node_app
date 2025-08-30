# 🚀 Node.js / MERN Application Deployment Guide on VPS (Ubuntu)

This guide provides step-by-step instructions to deploy Node.js or MERN applications on a VPS, including **performance optimization**, **PM2 process management**, **NGINX reverse proxy**, and **SSL setup**. It also includes notes for deploying a **Next.js frontend without a custom server**.

## 1. 🔑 Connect to VPS
```bash
ssh root@your_vps_ip
type ypur password
```

## 2. 🛠 Update & Upgrade Packages
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y build-essential curl git ufw
```

## 3. 📦 Install Node.js (Latest LTS)
```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | bash -
sudo apt install -y nodejs
node -v
npm -v
```

## 4. 🛡 Setup Firewall (UFW)
```bash
sudo ufw allow OpenSSH
sudo ufw allow http
sudo ufw allow https
sudo ufw enable
```

## 5. 📂 Clone Your Application
```bash
cd /var/www
git clone https://github.com/yourusername/your-node-app.git
cd your-node-app
npm install
```
