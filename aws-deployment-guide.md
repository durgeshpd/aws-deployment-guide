
# 🚀 Full Stack Project Deployment on AWS EC2 (Nginx + Node.js + PM2 + Frontend Build)

---

## 🔐 Step 1: Prepare Your SSH Key and Connect to EC2
1. Download your EC2 `.pem` key file from AWS.
2. Move it to a secure directory (e.g., `~/ec2-keys/`).
3. Set permissions:
   ```bash
   chmod 400 ~/ec2-keys/devConn-secret.pem
   ```
4. SSH into the EC2 instance:
   ```bash
   ssh -i "~/ec2-keys/devConn-secret.pem" ubuntu@ec2-51-21-190-33.eu-north-1.compute.amazonaws.com
   ```

---

## 🌐 Step 2: Install Nginx
```bash
sudo apt update
sudo apt install nginx -y
sudo systemctl enable nginx
sudo systemctl start nginx
```
Visit: http://51.21.190.33 — you should see the Nginx welcome page.

---

## 🧰 Step 3: Install Node.js and npm
```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs
node -v
npm -v
```

---

## 📦 Step 4: Clone Your Backend and Frontend Projects
```bash
git clone https://github.com/your-username/devconn-backend.git
git clone https://github.com/your-username/devconn-frontend.git
```

---

## 🛠️ Step 5: Set Up the Backend
```bash
cd devconn-backend
npm install
sudo npm install -g pm2
pm2 start index.js --name devconn-backend
pm2 save
```
> Replace `index.js` with your actual backend entry file if different.

---

## 🧱 Step 6: Build the Frontend
```bash
cd ~/devconn-frontend
npm install
npm run build
```
The build folder may be `dist`, `.next`, or `build`.

---

## 📂 Step 7: Deploy Frontend to Nginx
```bash
sudo rm -rf /var/www/html/*
sudo cp -r dist/* /var/www/html/
sudo chown -R www-data:www-data /var/www/html
```
> Replace `dist` with your actual build output folder.

---

## 🔧 Step 8: Configure Nginx
```bash
sudo nano /etc/nginx/sites-available/default
```
Paste the following:
```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    index index.html;

    server_name 51.21.190.33;

    location / {
        try_files $uri /index.html;
    }

    location /api/ {
        proxy_pass http://localhost:3000/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Restart Nginx:
```bash
sudo systemctl restart nginx
```

---

## ✅ Final: Test Your Application
Frontend: http://51.21.190.33

Backend/API: http://51.21.190.33/api/

📌 Notes
- Make sure your backend handles routes under `/api` if using that path.
- Use `pm2 logs devconn-backend` to check backend logs.
- Rebuild frontend after updates: `npm run build` → copy to `/var/www/html`.

---
