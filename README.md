
# 🚀 NGINX Reverse Proxy (Production Ready)
![NGINX](https://img.shields.io/badge/NGINX-ReverseProxy-green) ![Docker](https://img.shields.io/badge/Docker-Ready-blue) 
Reverse Proxy ด้วย **NGINX** สำหรับใช้งานจริง รองรับ Docker, Routing, Load Balancing และ HTTPS

## 📖 Overview
โปรเจคนี้ใช้ NGINX เป็น Reverse Proxy เพื่อจัดการ traffic ระหว่าง client และ backend services ช่วยให้คุณสามารถ:
* ✅ ซ่อน backend server (Security) 
* ✅ จัดการ routing ได้ง่าย 
* ✅ รองรับ microservices
* ✅ เพิ่ม security และ performance
 
### 🔍 Reverse Proxy คืออะไร?
**Reverse Proxy** คือ server ที่อยู่หน้าระบบ backend ทำหน้าที่รับ request จาก client แล้วส่งต่อไปยัง server ภายใน เพื่อเพิ่มความปลอดภัยและความเร็วในการจัดการ traffic
  
---
## ✨ Features
* 🔁 **Reverse Proxy:** จัดการ request อย่างมีประสิทธิภาพ 
* 📍 **Path-based routing:** แยกตาม path เช่น `/api`, `/` 
* 🧩 **Multi-backend:** รองรับหลาย backend พร้อมกัน 
* 🐳 **Docker ready:** ติดตั้งง่ายด้วย Docker Compose 
* ⚖️ **Load balancing:** รองรับการทำ Scaling 
* 🔐 **HTTPS (SSL/TLS):** รองรับความปลอดภัยระดับสูง 
* 🚀 **Performance:** ปรับแต่งมาเพื่อใช้งานในระดับ Production

## 🏗️ Architecture & Structure
### 📁 Project Structure
```text 
Reverse-Proxy/
|   docker-compose.yml
|   README.md
|
+---nginx
|   |   nginx.conf
|   |
|   +---conf.d
|   |   |   example_domain.conf
|   |   |
|   |   \---locations
|   |       +---example_domain
|   |       |       project1.conf
|   |       |
|   |       \---example_domain2
|   +---snippets
|   |       cors.conf
|   |       log-format.conf
|   |       proxy-api.conf
|   |       proxy-base.conf
|   |       proxy-ssl.conf
|   |       proxy-static.conf
|   |       proxy-ws.conf
|   |       security.conf
|   |       ssl.conf
|   |
|   +---ssl
|   |       ca_bundle.crt
|   |       example_domain.crt
|   |       example_domain.key
|   |
|   \---upstream
|           project1.conf
|
\---nginx_fronts
        project1.conf
```

## 1. โครงสร้างและการแบ่งหน้าที่ (Architectural Roles)

ระบบของคุณถูกแยกออกเป็นส่วนๆ เพื่อให้ง่ายต่อการดูแลรักษา:

-   **The Brain (`nginx.conf`)**: กำหนดค่าพื้นฐานของเซิร์ฟเวอร์ทั้งหมด เช่น การจัดการ Log, การจำกัดความเร็ว (Rate Limit) และการตั้งค่าประสิทธิภาพ (Worker Connections)
    
-   **The Gatekeeper (`conf.d/`)**: เป็นจุดรับ Traffic จากโลกภายนอก โดยแยกตามชื่อโดเมน (Domain Name) และจัดการเรื่อง SSL/TLS
    
-   **The Reusable Logic (`snippets/`)**: เก็บ "สูตรสำเร็จ" ที่ใช้บ่อยๆ เช่น การตั้งค่า Security, CORS, และการทำ Proxy สำหรับ WebSocket หรือ Static Files เพื่อลดการเขียนโค้ดซ้ำ
    
-   **The Router (`locations/`)**: แยกเส้นทางย่อยภายในโดเมน เช่น `/project1` เพื่อส่งต่อไปยังบริการที่ถูกต้อง
    
-   **The Address Book (`upstream/`)**: เก็บรายชื่อและ Port ของ Container หรือ Server จริงๆ ที่รันแอปพลิเคชันอยู่
    
-   **The Internal Frontend (`nginx_fronts/`)**: คอนฟิกเฉพาะทางสำหรับฝั่ง Client-side App (SPA) เพื่อให้รองรับการกด Refresh หน้าเว็บแล้วไม่เจอ Error 404
    

----------

## 2. เส้นทางการเดินทางของ Request (Request Lifecycle)

ลองจินตนาการว่ามีผู้ใช้งานเข้าชมเว็บไซต์ `example_domain.com/project1/` ระบบจะทำงานตามลำดับดังนี้ครับ:

#### **Step 1: การรับช่วงต่อ (Entrance)**

เมื่อ Request วิ่งเข้ามาที่ Port 443 (HTTPS) Nginx จะไปดูที่ไฟล์ `conf.d/example_domain.conf` เพื่อยืนยันว่าใบเซอร์ SSL ถูกต้อง และดึงค่าความปลอดภัยจาก `snippets/security.conf` มาสวมทับทันที

#### **Step 2: การคัดแยกเส้นทาง (Routing)**

Nginx จะมองหาเงื่อนไขใน `location` ซึ่งในกรณีนี้มันจะเข้าไปอ่านไฟล์ `locations/example_domain/project1.conf` แล้วพบว่า Request นี้ต้องถูกส่งต่อไปยังกลุ่ม Server ที่ชื่อว่า `project1_frontend`

#### **Step 3: การส่งต่ออย่างมีชั้นเชิง (Proxying)**

ก่อนจะส่งต่อ Nginx จะดึงเอา `snippets/proxy-static.conf` มาใช้ เพื่อตั้งค่าการพักข้อมูล (Buffering) และดึง `proxy-base.conf` มาเพื่อแนบ IP จริงของผู้ใช้ส่งต่อไปให้ Backend ด้วย

#### **Step 4: การเข้าถึงเป้าหมาย (Upstream to Container)**

Nginx เปิดดูไฟล์ `upstream/project1.conf` เพื่อหาที่อยู่ของ Container `another-project1-frontend:3000` แล้วส่ง Request เข้าไปข้างใน

#### **Step 5: การประมวลผลภายใน Container (Internal Frontend)**

ภายใน Container ของโปรเจกต์ 1 จะมี Nginx อีกตัวที่ใช้คอนฟิกจาก `nginx_fronts/project1.conf` ทำหน้าที่ตรวจสอบว่าผู้ใช้ขอไฟล์อะไร ถ้าเป็นหน้าเว็บทั่วไป มันจะส่ง `index.html` กลับออกมาผ่าน Proxy จนถึงมือผู้ใช้

----------

## 3. จุดเด่นและความได้เปรียบของโครงสร้างนี้

1.  **Scalability (การขยายตัว)**: หากคุณมีโปรเจกต์ที่ 2 คุณแค่สร้างไฟล์ใหม่ใน `locations/` และ `upstream/` โดยแทบไม่ต้องแตะต้องคอนฟิกเดิมเลย ลดความเสี่ยงในการทำระบบเดิมพัง (Regressions)
    
2.  **High Availability (ความพร้อมใช้งาน)**: ในไฟล์ `upstream` คุณมีการตั้งค่า `max_fails` และ `fail_timeout` ไว้ หากแอปตัวใดตัวหนึ่งค้างหรือล่ม Nginx จะหลบไปใช้ตัวอื่นที่ยังทำงานได้อัตโนมัติ
    
3.  **Observability (การตรวจสอบ)**: การที่คุณทำ `log_format` แบบละเอียด (แสดง `rt`, `uct`, `urt`) ทำให้คุณรู้ได้ทันทีว่าถ้าเว็บช้า ช้าที่ตัว Nginx เอง หรือช้าที่ Code ของโปรเจกต์นั้นๆ
    
4.  **Security (ความปลอดภัย)**: การแยก `snippets/security.conf` และ `ssl.conf` ออกมา ช่วยให้คุณมั่นใจว่าทุกโดเมนในเซิร์ฟเวอร์จะได้รับมาตรฐานความปลอดภัยระดับสูงสุด (A+ Grade) เหมือนกันทั้งหมด
    
5.  **WebSocket Support**: ระบบพร้อมรองรับงาน Real-time ทันทีผ่าน `proxy-ws.conf` ซึ่งมีการตั้งค่า Timeout ที่เหมาะสมไว้แล้ว (1 ชั่วโมง)


---
## 🔍 อธิบายการทำงานแบบเจาะลึก (Step-by-Step)

เมื่อมี Request วิ่งเข้ามา ระบบจะมองหาไฟล์ตามลำดับดังนี้:

#### **1. เริ่มที่หัวใจ: `nginx.conf`**

Nginx จะอ่านไฟล์นี้ก่อนเป็นอันดับแรก ซึ่งคุณเขียนคำสั่ง `include /etc/nginx/conf.d/*.conf;` ไว้ตอนท้าย

> **มันจึงวิ่งไปหา:** ไฟล์ในโฟลเดอร์ `conf.d/` ต่อทันที

#### **2. ระบุโดเมน: `conf.d/example_domain.conf`**

ไฟล์นี้จะบอกว่า "ถ้าใครเรียก `example.com` ให้มาคุยกับฉัน" ภายในไฟล์นี้จะมีการ `include` อีก 2 จุดสำคัญ:

-   **`include snippets/ssl.conf;`**: มันจะวิ่งไปดึงมาตรฐานการเข้ารหัสจากโฟลเดอร์ `snippets` มาใช้
    
-   **`include conf.d/locations/example_domain/*.conf;`**: มันจะวิ่งไปดูว่าในโดเมนนี้ มี "เส้นทางย่อย" (Paths) อะไรบ้าง
    

#### **3. ระบุเส้นทาง: `locations/example_domain/project1.conf`**

ไฟล์นี้จะบอกว่า "ถ้าใครเข้า `/project1/` ให้ส่งต่อไปที่ `project1_frontend`"

-   **การเชื่อมโยง:** ชื่อ `project1_frontend` นี้ Nginx จะไม่รู้จักถ้าคุณไม่ได้ทำ Upstream ไว้
    

#### **4. ระบุเป้าหมายปลายทาง: `upstream/project1.conf`**

นี่คือจุดที่ Nginx จะรู้ว่าต้องส่งข้อมูลไปที่ไหนจริง ๆ

-   มันจะอ่านไฟล์นี้แล้วพบว่า `project1_frontend` คือ Container ที่ชื่อ `another-project1-frontend` ที่พอร์ต `3000`


---

## 🔧 สิ่งที่ต้องมี

1.  **Docker**
    
2.  **Docker Compose**
    

## 📥 1. Clone Project
```
git clone https://github.com/devideatrade258/Reverse-Proxy.git
cd Reverse-Proxy
```

## 🐳 2. Run ด้วย Docker
```
docker-compose up -d
```

## ✅ 3. ตรวจสอบว่าใช้งานได้
เปิด browser ไปที่:
-   `http://localhost:3000` (Frontend)
-   `http://localhost:5000` (Backend API)


## 🛠 วิธีการเพิ่ม Project ใหม่ (Workflow)

หากต้องการเพิ่มโปรเจกต์ใหม่ (เช่น `project2`) ให้ทำตามขั้นตอนดังนี้:

### 1. สร้าง Upstream

สร้างไฟล์ `nginx/upstream/project2.conf`:

Nginx

```
upstream project2_backend {
    server container-name:port max_fails=3 fail_timeout=30s;
    keepalive 64;
}

```

### 2. สร้าง Location Routing

สร้างไฟล์ `nginx/conf.d/locations/your_domain/project2.conf`:

Nginx

```
location /project2/ {
    proxy_pass http://project2_backend;
    include snippets/proxy-api.conf; # หรือ proxy-static.conf
}

```

### 3. ตรวจสอบและ Re-config

รันคำสั่งตรวจสอบ Syntax และสั่งให้ Nginx โหลดคอนฟิกใหม่:

Bash

```
docker exec -it nginx-container nginx -t
docker exec -it nginx-container nginx -s reload
```


## 🔐 มาตรฐานความปลอดภัย (Security Standards)

ระบบนี้ถูกปรับแต่งให้ได้รับความปลอดภัยระดับ **A+** (อ้างอิง SSL Labs):

-   **HSTS**: บังคับใช้ HTTPS นาน 1 ปี พร้อม Subdomains
    
-   **TLS 1.2 / 1.3**: ปิด Protocol เก่าที่ล้าสมัย
    
-   **CORS Control**: จัดการการเข้าถึงข้าม Domain ผ่าน `snippets/cors.conf`
    
-   **Rate Limiting**: ป้องกันการโดน Bruteforce (ตั้งค่าไว้ที่ 20 req/s ต่อ IP)
    

----------

## 📊 การตรวจสอบระบบ (Monitoring & Logs)

ไฟล์ Log ถูกปรับแต่งให้อ่านง่ายและวิเคราะห์ประสิทธิภาพได้ (Format: `main_ext`):

-   `rt`: Request Time (เวลาทั้งหมดที่ผู้ใช้รอ)
    
-   `urt`: Upstream Response Time (เวลาที่ Backend ใช้ประมวลผล)
    
-   `up`: Upstream Address (บอกว่า Request นี้วิ่งไปลงที่ Container ไหน)
    

ตรวจสอบ Log แบบ Real-time:

Bash

```
docker logs -f nginx-container

```

----------

## 📦 Docker Compose Example

YAML

```
services:
  nginx-proxy:
    image: nginx:alpine
    container_name: reverse-proxy
    ports:
      - "80:80"
      - "443:443"    
    volumes: 
	    - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro 
	    - ./nginx/conf.d:/etc/nginx/conf.d:ro
	    - ./nginx/snippets:/etc/nginx/snippets:ro
	    - ./nginx/ssl:/etc/nginx/ssl:ro
	    - ./nginx/upstream:/etc/nginx/upstream:ro
	    # เพิ่มเติมส่วนนี้หากใน conf.d มีการ include ออกไปนอกโฟลเดอร์หลัก
    networks:
      - web_net
```