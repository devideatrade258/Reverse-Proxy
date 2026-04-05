🚀 NGINX Reverse Proxy (Production Ready)

Reverse Proxy ด้วย NGINX สำหรับใช้งานจริง รองรับ Docker, Routing, Load Balancing และ HTTPS

📖 Overview

โปรเจคนี้ใช้ NGINX เป็น Reverse Proxy เพื่อจัดการ traffic ระหว่าง client และ backend services

ช่วยให้คุณสามารถ:

ซ่อน backend server
จัดการ routing ได้ง่าย
รองรับ microservices
เพิ่ม security และ performance
🔍 Reverse Proxy คืออะไร?

Reverse Proxy คือ server ที่อยู่หน้าระบบ backend ทำหน้าที่รับ request จาก client แล้วส่งต่อไปยัง server ภายใน

✨ Features
🔁 Reverse Proxy (NGINX)
📍 Path-based routing (/api, /)
🧩 รองรับหลาย backend
🐳 Docker ready
⚖️ Load balancing (รองรับ scaling)
🔐 HTTPS (SSL/TLS)
🚀 Performance สูง (เหมาะ production)
🏗️ Architecture
📁 Project Structure
Reverse-Proxy/
│
├── nginx.conf
├── conf.d/
│   └── default.conf
├── docker-compose.yml
├── backend/
│   └── (your API service)
├── frontend/
│   └── (your web app)
└── ssl/
    └── (certificate files)
⚙️ วิธีติดตั้ง (Installation)
🔧 สิ่งที่ต้องมี
Docker
Docker Compose
📥 1. Clone Project
git clone https://github.com/devideatrade258/Reverse-Proxy.git
cd Reverse-Proxy
🐳 2. Run ด้วย Docker
docker-compose up -d
✅ 3. ตรวจสอบว่าใช้งานได้

เปิด browser:

http://localhost

หรือ

http://localhost/api
⚙️ Configuration (สำคัญมาก)
📌 ตัวอย่าง default.conf
server {
    listen 80;

    # API
    location /api/ {
        proxy_pass http://backend:3000/;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    # Frontend
    location / {
        proxy_pass http://frontend:80/;
    }
}
🧠 อธิบาย config ทีละส่วน
🔹 location /api/
ใช้จับ request ที่ขึ้นต้นด้วย /api
เช่น /api/users
🔹 proxy_pass
ส่ง request ไป backend
/api/users → backend:3000/users
🔹 headers

ช่วยให้ backend รู้ว่า request มาจาก client จริง

proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
▶️ การใช้งาน (Usage)
📌 ตัวอย่าง
http://localhost/api/users

NGINX จะส่งไป:

http://backend:3000/users
⚖️ Load Balancing
upstream backend {
    server backend1:3000;
    server backend2:3000;
}
location /api/ {
    proxy_pass http://backend;
}
🔐 HTTPS (SSL)
📌 config
server {
    listen 443 ssl;

    ssl_certificate /etc/nginx/ssl/cert.pem;
    ssl_certificate_key /etc/nginx/ssl/key.pem;

    location / {
        proxy_pass http://frontend;
    }
}
📌 ใช้ Let's Encrypt (แนะนำ)

ติดตั้ง Certbot:

sudo apt install certbot python3-certbot-nginx
🐳 ตัวอย่าง docker-compose.yml
version: '3'

services:
  nginx:
    image: nginx:latest
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./conf.d:/etc/nginx/conf.d
      - ./ssl:/etc/nginx/ssl
    depends_on:
      - backend
      - frontend

  backend:
    build: ./backend
    ports:
      - "3000:3000"

  frontend:
    build: ./frontend
    ports:
      - "80"
🧪 Testing
ใช้ curl
curl http://localhost/api
🛠️ Troubleshooting
❌ 502 Bad Gateway
backend ไม่ทำงาน
port ไม่ตรง
❌ 404 Not Found
path ไม่ match location
❌ Connection Refused
Docker network มีปัญหา
🔍 ดู log
docker logs nginx
🚀 Optimization (แนะนำสำหรับ production)
🔹 เปิด gzip
gzip on;
gzip_types text/plain application/json;
🔹 เพิ่ม timeout
proxy_connect_timeout 60;
proxy_read_timeout 60;
🔹 Rate Limiting
limit_req_zone $binary_remote_addr zone=api_limit:10m rate=10r/s;
🔮 Future Improvements
🔐 JWT Authentication
📊 Monitoring (Prometheus + Grafana)
💾 Caching (Redis / NGINX cache)
🚦 Rate Limiting ขั้นสูง
🌍 Multi-domain routing
🧠 Use Cases
API Gateway
Microservices
Web Hosting
Load Balancer
Security Layer
👨‍💻 Author

devideatrade258

📄 License

MIT License

💡 Pro Tips
ใช้ domain จริง + SSL เสมอ
แยก environment (dev / staging / prod)
ใช้ CI/CD deploy อัตโนมัติ
backup config ทุกครั้งก่อนแก้
⭐ Bonus (แนะนำให้เพิ่มใน GitHub)

เพิ่ม badge:

![NGINX](https://img.shields.io/badge/NGINX-ReverseProxy-green)
![Docker](https://img.shields.io/badge/Docker-Ready-blue)

ถ้าคุณอยาก “โหดขึ้นอีกระดับ” เช่น:

🔥 ทำเป็น Kubernetes (Ingress + NGINX)
🔥 ทำระบบ auto deploy
🔥 ใส่ domain จริง + Cloudflare

บอกผมได้เลย เดี๋ยวผมจัดให้ระดับ production จริงแบบบริษัทใช้ 🚀