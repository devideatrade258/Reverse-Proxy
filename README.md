<project>
    <name>NGINX Reverse Proxy</name>
    <repository>https://github.com/devideatrade258/Reverse-Proxy.git</repository>

    <description>
        โปรเจคนี้เป็นการใช้งาน NGINX เพื่อทำหน้าที่เป็น Reverse Proxy
        สำหรับจัดการ HTTP/HTTPS request และส่งต่อไปยัง backend services
        เช่น Web Application หรือ API Server
    </description>

    <overview>
        <nginx>
            NGINX เป็น web server และ reverse proxy ที่มีประสิทธิภาพสูง
            รองรับ concurrent connection จำนวนมาก และนิยมใช้ใน production
        </nginx>

        <reverse_proxy>
            Reverse Proxy คือการที่ client ติดต่อกับ proxy server แทน backend จริง
            โดย proxy จะเป็นตัวจัดการ routing และส่ง request ไปยัง server ภายใน
        </reverse_proxy>

        <benefits>
            <item>เพิ่มความปลอดภัย (ซ่อน backend)</item>
            <item>รองรับ load balancing</item>
            <item>จัดการ routing ได้ง่าย</item>
            <item>รองรับ HTTPS termination</item>
            <item>เพิ่ม performance ด้วย caching</item>
        </benefits>
    </overview>

    <architecture>
        <diagram>
            Client → NGINX → Backend Server(s)
        </diagram>

        <flow>
            <step>Client ส่ง request มายัง NGINX</step>
            <step>NGINX ตรวจสอบ URL / path</step>
            <step>NGINX เลือก backend ตาม config</step>
            <step>Forward request ไปยัง backend</step>
            <step>รับ response แล้วส่งกลับ client</step>
        </flow>
    </architecture>

    <project_structure>
        <files>
            <file>
                <name>nginx.conf</name>
                <description>
                    ไฟล์หลักสำหรับ config NGINX เช่น worker, events, http
                </description>
            </file>

            <file>
                <name>conf.d/default.conf</name>
                <description>
                    ใช้กำหนด reverse proxy rules เช่น location และ proxy_pass
                </description>
            </file>

            <file>
                <name>docker-compose.yml</name>
                <description>
                    ใช้สำหรับ run NGINX และ backend services ด้วย Docker
                </description>
            </file>

            <file>
                <name>html/</name>
                <description>
                    static files (ถ้ามี)
                </description>
            </file>
        </files>
    </project_structure>

    <core_configuration>
        <example>
            <![CDATA[
server {
    listen 80;

    location /api/ {
        proxy_pass http://backend:3000/;
        
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    location / {
        proxy_pass http://frontend:80/;
    }
}
            ]]>
        </example>
    </core_configuration>

    <how_it_works>
        <step number="1">Client เรียก http://your-domain/api</step>
        <step number="2">NGINX ตรวจจับ path /api</step>
        <step number="3">Forward ไป backend:3000</step>
        <step number="4">Backend ประมวลผล</step>
        <step number="5">ส่ง response กลับผ่าน NGINX</step>
    </how_it_works>

    <installation>
        <method>Docker</method>

        <steps>
            <step>git clone https://github.com/devideatrade258/Reverse-Proxy.git</step>
            <step>cd Reverse-Proxy</step>
            <step>docker-compose up -d</step>
        </steps>
    </installation>

    <usage>
        <examples>
            <example>
                <![CDATA[
# เรียก API ผ่าน proxy
http://localhost/api/users

# NGINX จะส่งไป
http://backend:3000/users
                ]]>
            </example>
        </examples>
    </usage>

    <features>
        <feature>Reverse Proxy ด้วย NGINX</feature>
        <feature>Routing ตาม path (/api, /)</feature>
        <feature>รองรับหลาย backend</feature>
        <feature>รองรับ Docker deployment</feature>
        <feature>รองรับการขยายเป็น Load Balancer</feature>
    </features>

    <advanced_features>
        <load_balancing>
            <![CDATA[
upstream backend {
    server backend1:3000;
    server backend2:3000;
}
            ]]>
        </load_balancing>

        <https>
            <![CDATA[
server {
    listen 443 ssl;
    ssl_certificate /etc/nginx/ssl/cert.pem;
    ssl_certificate_key /etc/nginx/ssl/key.pem;
}
            ]]>
        </https>
    </advanced_features>

    <use_cases>
        <case>API Gateway</case>
        <case>Microservices Architecture</case>
        <case>Web Hosting</case>
        <case>Load Balancer</case>
    </use_cases>

    <troubleshooting>
        <issue>
            <problem>502 Bad Gateway</problem>
            <solution>ตรวจสอบ backend server ว่าทำงานอยู่หรือไม่</solution>
        </issue>

        <issue>
            <problem>404 Not Found</problem>
            <solution>ตรวจสอบ path ใน location และ proxy_pass</solution>
        </issue>

        <issue>
            <problem>Connection Refused</problem>
            <solution>ตรวจสอบ port และ network (Docker)</solution>
        </issue>
    </troubleshooting>

    <future_improvements>
        <item>เพิ่มระบบ Authentication (JWT)</item>
        <item>เพิ่ม Rate Limiting</item>
        <item>เพิ่ม Cache</item>
        <item>เพิ่ม Monitoring (Prometheus)</item>
    </future_improvements>

    <author>
        <name>devideatrade258</name>
    </author>

    <license>
        MIT License
    </license>
</project>