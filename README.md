# Node.js / MERN Application Deployment Guide on VPS (Ubuntu)

This guide provides step-by-step instructions to deploy Node.js or MERN applications on a VPS, including **performance optimization**, **PM2 process management**, **NGINX reverse proxy**, and **SSL setup**. It also includes notes for deploying a **Next.js frontend without a custom server**.

## 1. Connect to VPS
```bash
ssh root@your_vps_ip
type your password
```

## 2. Update & Upgrade Packages
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y build-essential curl git ufw
```

## 3. Install Node.js (Latest LTS)
```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | bash -
sudo apt install -y nodejs
node -v
npm -v
```

## 4. Setup Firewall (UFW)
```bash
sudo ufw allow OpenSSH
sudo ufw allow http
sudo ufw allow https
sudo ufw enable
```

## 5. Clone Your Application
```bash
cd /var/www
git clone https://github.com/yourusername/your-node-app.git
cd your-node-app
npm install
```

## 6. Setup Environment Variables
Create a .env file in your project root:
```bash
nano .env
```
Add database URLs, API keys, secrets, and other environment variables.

## 7. Start Application with PM2
Backend / Node.js
```bash
pm2 start server.js --name myapp
pm2 save
pm2 startup
```
Frontend (Next.js without custom server) <br />
Note: If your Next.js app does not use a custom server, run it using npm start instead of server.js:
```bash
pm2 start npm --name "frontend" -- start
```
Default port: 3000 <br />
Optional clustering for high traffic:
```bash
pm2 start npm --name "frontend" -- start -- -p 3000 -i max
```

## 8. Setup NGINX Reverse Proxy
```bash
sudo apt install nginx -y
sudo nano /etc/nginx/sites-available/myapp
```
Example configuration:
```bash
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://localhost:3000; # Change port if needed
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    # Enable gzip & caching for static files
    gzip on;
    gzip_types text/plain application/javascript application/x-javascript text/javascript text/xml text/css application/json;

    location ~* \.(jpg|jpeg|png|gif|ico|css|js|woff|woff2|svg)$ {
        expires 30d;
        access_log off;
        add_header Cache-Control "public";
    }
}
```
Enable the site and reload NGINX:
```bash
sudo ln -s /etc/nginx/sites-available/myapp /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

## 9. Add SSL with Let’s Encrypt
```bash
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
sudo systemctl enable certbot.timer
```

## 10. Performance Optimization
PM2 Cluster Mode
```bash
pm2 start server.js -i max
```
Enable Gzip & Static Caching (configured in NGINX above)
Node.js Compression Middleware
```bash
const compression = require("compression");
app.use(compression());
```
Optimize database queries, indexes, and consider Redis caching for heavy reads.

## 11. Monitoring
View Logs:
```bash
pm2 logs myapp
```
Monitor app and resource usage:
```bash
pm2 monit
```

## 12. Optional CI/CD Deployment
Automate deployment with GitHub Actions or GitLab CI/CD:
```bash
git pull origin main
npm install
npm run build
pm2 restart myapp
```

## 13. Final Checklist
```bash
Node.js installed & app running under PM2

NGINX reverse proxy configured

SSL enabled with Let’s Encrypt

Firewall enabled

Logs & monitoring active

Performance optimizations applied (gzip, caching, clustering)
```

Notes:

```bash
This guide works for MERN stack apps, backend APIs (Express/Node.js), and frontend apps (Next.js without custom server).

For Next.js frontend, using npm start is sufficient if no custom server is needed.

For high traffic apps, consider PM2 cluster mode and NGINX caching.
```
