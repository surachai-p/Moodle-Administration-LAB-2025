## 2. ทฤษฎีและหลักการ

### 2.1 Docker และ Container Technology

#### 2.1.1 Docker คือ อะไร?

**Docker** เป็นแพลตฟอร์มสำหรับพัฒนา ส่งมอบ และรันแอปพลิเคชันในรูปแบบ Container โดย Container เป็นหน่วยมาตรฐานของซอฟต์แวร์ที่บรรจุโค้ด รันไทม์ ไลบรารี และการตั้งค่าทั้งหมดที่จำเป็นไว้ด้วยกัน

**ข้อดีของ Docker:**
- **Portability** - รันได้ทุกที่ที่มี Docker
- **Lightweight** - เบากว่า Virtual Machine
- **Isolation** - แอปพลิเคชันแต่ละตัวแยกกัน
- **Version Control** - จัดการ Image ได้เหมือน Code
- **Fast Deployment** - เริ่มต้นและหยุดได้รวดเร็ว

#### 2.1.2 Container vs Virtual Machine
![Virtual Machine and Container](images/vs_container.png)


#### 2.1.3 Docker Components

1. **Docker Image** - เทมเพลตที่ใช้สร้าง Container (อ่านอย่างเดียว)
2. **Docker Container** - Instance ที่รันจาก Image (อ่าน-เขียนได้)
3. **Docker Volume** - พื้นที่จัดเก็บข้อมูลถาวร
4. **Docker Network** - ช่องทางสื่อสารระหว่าง Container
5. **Docker Compose** - เครื่องมือจัดการ Multi-container

### 2.2 Docker Compose

**Docker Compose** เป็นเครื่องมือสำหรับกำหนดและรัน Multi-container Application โดยใช้ไฟล์ YAML เพียงไฟล์เดียว

**โครงสร้างไฟล์ docker-compose.yml:**
```yaml
version: '3.8'        # เวอร์ชันของ Compose file format
services:             # รายการ Container ที่ต้องการรัน
  service1:           # ชื่อ Service
    image:            # Docker Image ที่ใช้
    ports:            # Port mapping
    environment:      # Environment variables
    volumes:          # Volume mounting
    networks:         # Network ที่เชื่อมต่อ
networks:             # กำหนด Network
volumes:              # กำหนด Volume
```

### 2.3 Docker Networking

Docker มี Network Driver หลายประเภท:

1. **Bridge Network** (ใช้ในการทดลองนี้)
   - เป็น Virtual Network ภายในเครื่อง Host
   - Container สามารถสื่อสารกันผ่าน **Container name**
   - เหมาะสำหรับ Application บนเครื่องเดียว

2. **Host Network**
   - ใช้ Network ของ Host โดยตรง
   - ไม่มีการแยก Network namespace

3. **Overlay Network**
   - สำหรับ Multi-host networking
   - ใช้ใน Docker Swarm

**ตัวอย่างการสื่อสาร:**
```
Container A (moodle_app)  --[Bridge Network]--> Container B (moodle_db)
   hostname: moodle              hostname: db
```

### 2.4 Docker Volumes

**Volume** เป็นพื้นที่จัดเก็บข้อมูลที่แยกออกจาก Container Filesystem

**ประเภทของ Data Storage:**

1. **Anonymous Volume** - Docker สร้างและจัดการเอง
2. **Named Volume** - ตั้งชื่อและจัดการได้ (ใช้ในการทดลองนี้)
3. **Bind Mount** - Mount โฟลเดอร์จาก Host เข้า Container

**ข้อดีของ Named Volume:**
- ข้อมูลยังคงอยู่แม้ลบ Container
- แยก Lifecycle ของข้อมูลจาก Container
- สามารถ Backup และ Restore ได้
- Share ข้อมูลระหว่าง Container

### 2.5 Moodle LMS Architecture

#### 2.5.1 Moodle คืออะไร?

**Moodle** (Modular Object-Oriented Dynamic Learning Environment) เป็นระบบจัดการเรียนการสอนออนไลน์ (Learning Management System - LMS) แบบ Open Source ที่ได้รับความนิยมสูงสุดในโลก

**คุณสมบัติหลัก:**
- รองรับการสอนแบบหลากหลาย (Blended Learning, E-Learning)
- ระบบการให้คะแนนและประเมินผล
- Forum, Quiz, Assignment, Wiki
- Plugin มากกว่า 1,800 ตัว
- รองรับหลายภาษา

#### 2.5.2 Moodle System Architecture
![Moodle System Architecture](images/moodle_architecture.png)


### 2.5 ระบบที่ใช้ในการทดลอง: lthub/moodle

**lthub/moodle** เป็น Docker Image ที่สร้างขึ้นโดยมหาวิทยาลัย University of British Columbia (UBC) สำหรับใช้ใน Production

**ข้อมูลของ Image:**
- **Base OS:** Debian/Ubuntu
- **Web Server:** Apache 2.4
- **PHP:** PHP 8.x with required extensions
- **Architecture:** รองรับทั้ง AMD64 และ ARM64
- **Downloads:** 5M+ ครั้ง
- **Update:** อัพเดตสม่ำเสมอตาม Moodle releases

**Environment Variables ที่สำคัญ:**
- `MOODLE_DB_TYPE` - ชนิดของ Database (mariadb, mysql, pgsql)
- `MOODLE_DB_HOST` - ชื่อ hostname ของ Database
- `MOODLE_DB_NAME` - ชื่อ Database
- `MOODLE_DB_USER` - Username สำหรับเชื่อมต่อ DB
- `MOODLE_DB_PASSWORD` - รหัสผ่านสำหรับเชื่อมต่อ DB

---

## 3. สถาปัตยกรรมของระบบในการทดลอง

### 3.1 System Architecture Diagram

![System Architecture](images/architecture3.png)


### 3.2 Volume และ Data Persistence
![Volume and Data Persistence](images/volumedata.jpg)


### 3.3 Component Interaction Matrix

| From Component | To Component | Protocol | Port | Purpose |
|----------------|--------------|----------|------|---------|
| User Browser | Host Machine | HTTP | 80 | Web Interface Access |
| Host Machine | moodle_app | HTTP | 80 | Port Forwarding |
| moodle_app | moodle_db | MySQL | 3306 | Database Queries |
| moodle_app | Volume: moodledata | File I/O | - | File Storage |
| moodle_db | Volume: db_data | File I/O | - | Database Storage |
| moodle_app | moodle_app | Internal | - | PHP Processing |

---

## 4. อุปกรณ์และซอฟต์แวร์

### 4.1 ข้อกำหนดของระบบ

#### Hardware Requirements
- **CPU:** 2 cores ขึ้นไป (แนะนำ 4 cores)
- **RAM:** 4 GB ขึ้นไป (แนะนำ 8 GB)
- **Storage:** 20 GB พื้นที่ว่าง
- **Network:** Internet connection สำหรับดาวน์โหลด Images

#### Software Requirements
- **Operating System:** 
  - Windows 10/11 (64-bit) with WSL 2
  - macOS 10.15 ขึ้นไป
  - Linux (Ubuntu 20.04+, Debian, CentOS, Fedora)
- **Docker Desktop:** Version 4.0+ 
  - [ดาวน์โหลด Docker Desktop](https://www.docker.com/products/docker-desktop)
- **Text Editor:** Visual Studio Code
- **Web Browser:** Chrome, Firefox, Safari หรือ Edge (เวอร์ชันล่าสุด)

### 4.2 การติดตั้ง Docker Desktop

#### สำหรับ Windows:
1. เปิดใช้งาน WSL 2
2. ดาวน์โหลด Docker Desktop
3. ติดตั้งและรีสตาร์ท
4. ตรวจสอบ: `docker --version`

#### สำหรับ macOS:
1. ดาวน์โหลด Docker Desktop (Intel chip หรือ Apple Silicon)
2. ลาก Docker.app ไปยัง Applications
3. เปิด Docker Desktop
4. ตรวจสอบ: `docker --version`

#### สำหรับ Linux:
```bash
# Ubuntu/Debian
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
```

---

## 5. ขั้นตอนการปฏิบัติการทดลอง

### ขั้นตอนที่ 1: เตรียมไฟล์ Docker Compose

#### 1.1 สร้างโฟลเดอร์สำหรับโปรเจกต์

```bash
# สร้างโฟลเดอร์
mkdir moodle-docker
cd moodle-docker
```


#### 1.2 สร้างไฟล์ docker-compose.yml

ใช้ Text Editor สร้างไฟล์ `docker-compose.yml` ดังนี้:

```yaml
services:
  db:
    image: mariadb:latest
    container_name: moodle_db
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword # แก้ไขรหัสผ่านให้เป็นของตนเอง
      MYSQL_DATABASE: moodle
      MYSQL_USER: moodleuser
      MYSQL_PASSWORD: moodlepassword # แก้ไขรหัสผ่านให้เป็นของตนเอง
    volumes:
      - db_data:/var/lib/mysql # กำหนด volume แบบ Name volume
    networks:
      - moodle_network
    restart: unless-stopped

  moodle:
    image: lthub/moodle:education-4.5.8  # หากไม่สามารถโหลด image นี้ได้ ให้ลองใช้ lthub/moodle:latest หรือระบุ platform ตามบรรทัดด้านล่าง
    #platform: linux/arm64   # กรณีใช้บน Macbook สามารถใช้ image lthub/moodle:education-4.5.8 ได้ โดยระบุ platform (เอาเครื่องหมาย # ด้านหน้าออก)
    container_name: moodle_app
    ports:
      - "80:80" # กรณีที่เครื่องมี Web Server ที่ใช้งาน Port 80 อยู่แล้ว ให้เปลี่ยน Port เป็น "8080:80"  และการใช้งานต้องระบุ Port เป็น //localhost:8080
    environment:
      MOODLE_DB_TYPE: mariadb
      MOODLE_DB_HOST: db # ระบุเป็น db เพราะให้เชื่อมต่อไปยัง container name ที่ชื่อ db
      MOODLE_DB_NAME: moodle
      MOODLE_DB_USER: moodleuser
      MOODLE_DB_PASSWORD: moodlepassword # แก้ไขรหัสผ่านให้เป็นของตนเอง ให้ตรงกับรหัสผ่าน MYSQL_PASSWORD ด้านบน
      MOODLE_URL: 'http://localhost'
      
    volumes:
      - moodledata:/moodledata
    depends_on:
      - db
    links:
      - db
    networks:
      - moodle_network
    restart: unless-stopped

networks:
  moodle_network:
    driver: bridge

volumes:
  db_data:
  moodledata:
```

**อธิบายส่วนประกอบ:**

**Services:**
- `db`: MariaDB database server
- `moodle`: Moodle application server

**Environment Variables:**
- `MYSQL_ROOT_PASSWORD`: รหัสผ่าน root ของ MariaDB
- `MYSQL_DATABASE`: ชื่อ database ที่สร้างอัตโนมัติ
- `MYSQL_USER`: username สำหรับ Moodle
- `MYSQL_PASSWORD`: password สำหรับ Moodle
- `MOODLE_DB_TYPE`: ชนิดของ database
- `MOODLE_DB_HOST`: ชื่อ hostname ของ database (ชี้ไปที่ service `db`)
- `MOODLE_URL`: http://localhost

**Volumes:**
- `db_data`: เก็บข้อมูล MariaDB
- `moodledata`: เก็บไฟล์ Moodle

**Networks:**
- `moodle_network`: Bridge network สำหรับให้ Container สื่อสารกัน

**Restart Policy:**
- `unless-stopped`: รีสตาร์ทอัตโนมัติเมื่อ Docker เริ่มทำงาน

#### 1.3 ตรวจสอบไฟล์

```bash
# ตรวจสอบว่าไฟล์อยู่ในโฟลเดอร์
ls -la

# ตรวจสอบเนื้อหาไฟล์
cat docker-compose.yml
```

---

### ขั้นตอนที่ 2: ดาวน์โหลดและรัน Docker Containers

#### 2.1 ดาวน์โหลด Docker Images

```bash
# ทดสอบการดาวน์โหลด Images (docker-compose จะดาวน์โหลด image โดยอัตโนมัติ ขั้นตอนนี้ใช้เพื่อทดสอบว่าสามารถโหลด image ได้หรือไม่ หากไม่ต้องต้องเปลี่ยนขื่อ image ใหม่ และแก้ไขในไฟล์ docker-compose.yml ให้ชื่อ image ตรงกับที่สามารถโหลดได้จริง )
docker pull lthub/moodle:education-4.5.8 #หากไม่สามารถโหลดได้ ให้ลองโหลด lthub/moodle:latest หรือดูรายชื่อ image ที่มีใน docker hub
docker pull mariadb:latest
```

**บันทึกผลการทดลอง 1:**

<img width="722" height="128" alt="image" src="https://github.com/user-attachments/assets/5ba8352c-5151-4517-8c47-b1561f440e61" />

#### 2.2 รัน Docker Compose

```bash
docker-compose up -d
```

**คำอธิบาย:**
- `up`: สร้างและเริ่ม containers
- `-d`: รันในโหมด detached (background)

**ตรวจสอบการทำงานโดยใช้คำสั่ง docker-compose logs -f:**

**บันทึกผลการทดลอง 2:**

<img width="571" height="113" alt="image" src="https://github.com/user-attachments/assets/28d4c015-80d3-49e8-8ccd-0dca0a2fb77d" />


#### 2.3 ตรวจสอบสถานะ Containers

```bash
docker-compose ps
```

**ผลลัพธ์ควรแสดงดังรูป:**
```
    Name                  Command               State          Ports
---------------------------------------------------------------------------
moodle_app    /entrypoint.sh apache2-for ...   Up      0.0.0.0:80->80/tcp
moodle_db     docker-entrypoint.sh mariadbd    Up      3306/tcp
```
**คำอธิบาย:**
- **State: Up** = Container กำลังทำงาน
- **Ports** = Port mapping ที่ใช้งาน
  
**บันทึกผลการทดลอง 3:**

<img width="1188" height="106" alt="image" src="https://github.com/user-attachments/assets/aca279a4-14ec-4068-9375-aac1614b1607" />

#### 2.4 ตรวจสอบ Logs

```bash
# ดู Log ของ Moodle
docker-compose logs -f moodle

# กด Ctrl+C เพื่อหยุดดู Log
```

**รอจนกว่าจะเห็นข้อความ:**
- "Apache/2.x.x configured"
- "Server ready"

**กรณีที่ใช้ image moodle:latest แล้วมี erors ที่ log ให้ทดสอบรันคำสั่งดังนี้
```bash
docker exec -it moodle_app rm -f /etc/apache2/mods-enabled/shib.load /etc/apache2/mods-enabled/shib2.load
docker exec -it moodle_app sh -c "grep -rnl 'shib' /etc/apache2/ | xargs sed -i 's/^/### /g' 2>/dev/null"
docker exec -it moodle_app a2dismod brotli.load macro.load shib.load
docker exec -it moodle_app rm -f /etc/apache2/mods-enabled/shib.load
docker stop moodle_app
docker start moodle_app

```
**บันทึกผลการทดลอง 4:**

<img width="1202" height="317" alt="image" src="https://github.com/user-attachments/assets/a08d3a8a-a270-408d-be2a-c7a880f0548a" />


#### 2.5 ตรวจสอบ Network และ Volumes

```bash
# ตรวจสอบ Network
docker network ls
docker network inspect moodle-docker_moodle_network

# ตรวจสอบ Volumes
docker volume ls
docker volume inspect moodle-docker_moodledata
```

**บันทึกผลการทดลอง 5:**

<img width="1193" height="486" alt="image" src="https://github.com/user-attachments/assets/313d1ea1-de78-44eb-b4e8-51fb7334f092" />


---

### ขั้นตอนที่ 3: ติดตั้ง Moodle ผ่าน Web Interface

#### 1 เข้าสู่หน้าติดตั้ง

1. เปิด Web Browser
2. ไปที่: `http://localhost`  **กรณที่ใช้ port 8080 ให้ใช้**  `http://localhost:8080`
3. กรอกข้อมูลตามรายละเอียดต่าง ๆ 

กรอกข้อมูลผู้ดูแลระบบ:

**ข้อมูลบัญชี:**
| Field | Value | หมายเหตุ |
|-------|-------|----------|
| Username | `admin` | **ไม่สามารถเปลี่ยนได้** |
| Password | `Admin@2025!` | ต้องมีตัวพิมพ์ใหญ่, ตัวเลข, อักขระพิเศษ |
| First name | `ชื่อนักศึกษา` | ชื่อจริง |
| Surname | `นามสกุลนักศึกษา` | นามสกุล |
| Email address | `อีเมล์นักศึกษา` | อีเมล (ใช้งานจริงได้) |
| City/town | `ข้อมูลของนักศึกษา` | จังหวัด |
| Country | `Thailand` | ประเทศ |

**ข้อกำหนดรหัสผ่าน:**
- ความยาวอย่างน้อย 8 ตัวอักษร
- มีตัวพิมพ์ใหญ่อย่างน้อย 1 ตัว
- มีตัวพิมพ์เล็กอย่างน้อย 1 ตัว
- มีตัวเลขอย่างน้อย 1 ตัว
- มีอักขระพิเศษอย่างน้อย 1 ตัว


4.กรอกข้อมูลเว็บไซต์:

| Field | Value |
|-------|-------|
| Full site name | `ชื่อ-นามสกุล นักศึกษา-Moodle LMS` |
| Short name for site | `ชื่อนักศึกษา-Moodle` |
| Front page summary | `ชื่อนักศึกษา-Learning Management System for Education` |

5. ตั้งค่าอื่น ๆ เพิ่มเติม 
- **Self registration:** เลือก `No` หรือ `Email-based self-registration`
- **Default timezone:** `Asia/Bangkok`
- **Default language:** `English` หรือ `ภาษาไทย`

หลังจากบันทึกแล้ว ระบบจะแสดงหน้า **Dashboard** ของระบบ

**บันทึกผลการทดลอง 6:**


<img width="1908" height="1014" alt="image" src="https://github.com/user-attachments/assets/46a01b9c-9d8c-47cf-8b29-a43ac6d33cd0" />


---

## 6. การตั้งค่าและดูแลระบบ

### 6.1 การตั้งค่าระบบพื้นฐาน (Site Administration)

#### 6.1.1 เข้าสู่ Site Administration

1. คลิกที่ **รูปโปรไฟล์** มุมขวาบน
2. เลือก **Site administration**


#### 6.1.2 ตั้งค่า Location Settings

**เมนู:** Site administration → General-> Location -> location settings

**การตั้งค่าที่แนะนำ:**

| Setting | Value | คำอธิบาย |
|---------|-------|----------|
| Default timezone | `Asia/Bangkok` | เขตเวลาของไทย |
| Default country | `Thailand` | ประเทศเริ่มต้น |
| Force timezone | `No` | ให้ผู้ใช้เลือกเขตเวลาเองได้ |

**ขั้นตอน:**
1. ไปที่ Location settings
2. เลือก Default timezone: **Asia/Bangkok**
3. เลือก Default country: **Thailand**
4. คลิก **Save changes**

#### 6.1.3 ตั้งค่า Language

**เมนู:** Site administration → General-> Language → Language settings

**การตั้งค่า:**
1. **Default language:** English หรือ ภาษาไทย
2. **Display language menu:** Yes (ให้ผู้ใช้สลับภาษาได้)
3. **Languages on language menu:** เลือกภาษาที่ต้องการ

**ติดตั้งภาษาไทย (ถ้าต้องการ):**
1. ไปที่ Site administration → General -> Language → Language packs
2. เลือก **ภาษาไทย (th)**
3. คลิก **Install selected language pack**
4. รอจนกว่าจะติดตั้งเสร็จ


#### 6.1.4 ตั้งค่า Email

**เมนู:** Site administration → Server → Email → Outgoing mail configuration

**สำหรับการทดสอบ (ไม่ส่งอีเมลจริง):**
- **SMTP hosts:** (เว้นว่าง)
- **SMTP security:** None
- **No-reply address:** noreply@localhost

**สำหรับใช้งานจริง (ตัวอย่าง Gmail):**
```
SMTP hosts: smtp.gmail.com:587
SMTP security: TLS
SMTP username: your-email
SMTP password: your-app-password
No-reply: your-email
```

#### 6.1.5 ตั้งค่า Security

**Password Policy**

เมนู: Site administration → General -> Security → Site security settings

| Setting | Recommended Value |
|---------|-------------------|
| Password length | 8 |
| Digits | 1 |
| Lowercase letters | 1 |
| Uppercase letters | 1 |
| Non-alphanumeric | 1 |
| Password rotation limit | 5 |

**IP Blocker** (ถ้าต้องการ Block IP)

เมนู: Site administration → Security → IP blocker
- กำหนด IP ที่ต้องการบล็อก

#### 6.1.6 การตั้งค่า Appearance

#### 1 เปลี่ยน Theme

1. Site administration → Appearance → Themes → Theme selector
2. เลือก Theme ที่ชอบ:
   - **Boost:** ธีมเริ่มต้น (แนะนำ)
   - **Classic:** ธีมคลาสสิก
   - **More themes:** ดาวน์โหลดเพิ่ม

3. คลิก **Use theme**


#### 2 ปรับแต่ง Theme

**Boost Theme Settings:**

Site administration → Appearance → Themes → Boost → Settings **(กดที่รูปเฟือง)**

| Setting | Description |
|---------|-------------|
| Preset | สีและรูปแบบ (Default, Plain) |
| Brand color | สีหลักของเว็บไซต์ |
| Raw initial SCSS | CSS เพิ่มเติม |
| Raw SCSS | CSS เพิ่มเติม (หลังประมวลผล) |

**ตัวอย่าง Custom CSS: (อยู่ที่แท็ป Advanced settings)**
```scss
// เปลี่ยนสีหลัก
$primary: #0066cc;

// เปลี่ยนฟอนต์
body {
  font-family: 'Sarabun', sans-serif;
}

// ปรับขนาดหัวข้อ
h1 {
  font-size: 2.5rem;
}
```

#### 3 อัพโหลด Logo

1. Site administration → Appearance → Logos
2. อัพโหลด:
   - **Logo:** แสดงใน header
   - **Compact logo:** แสดงเมื่อ sidebar ถูกย่อ
   - **Favicon:** ไอคอนใน browser tab

**ขนาดที่แนะนำ:**
- Logo: 300x80 px (PNG)
- Compact logo: 50x50 px (PNG)
- Favicon: 32x32 px (ICO/PNG)

**บันทึกผลการทดลอง 6-1:**

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/3b0510af-97ea-4dca-9290-c5532e4d14b0" />

---

### 6.2 การจัดการ Course Categories และ Courses

#### 6.2.1 สร้าง Course Category

**ทฤษฎี:**
Course Category ใช้จัดกลุ่มรายวิชาตามหมวดหมู่ เช่น:
- คณะ/ภาควิชา
- ระดับชั้น
- ประเภทหลักสูตร
- ภาคการศึกษา

**ขั้นตอน:**

1. ไปที่ **Site administration → Courses → Manage courses and categories**

2. คลิก **Create new category**

3. กรอกข้อมูล:

| Field | Example Value | คำอธิบาย |
|-------|---------------|----------|
| Parent category | Top | หมวดหมู่แม่ |
| Category name | วิทยาศาสตร์และเทคโนโลยี | ชื่อหมวดหมู่ |
| Category ID number | SCI-TECH | รหัสหมวดหมู่ (ไม่ซ้ำ) |
| Description | หมวดวิชาทางด้านวิทยาศาสตร์และเทคโนโลยี | คำอธิบาย |

4. คลิก **Create category**

**สร้างกลุ่มวิชาเพิ่มเติม โดยใช้กลุ่มสาระที่มีของ สพฐ.:**

**บันทึกผลการทดลอง 7:**

<img width="1919" height="1046" alt="image" src="https://github.com/user-attachments/assets/5e2309c6-bcf2-4e20-8408-273e57c60810" />


#### 6.2.2 สร้าง Course (รายวิชา)

**ทฤษฎี:**
Course คือรายวิชาหรือหลักสูตรที่ใช้จัดการเรียนการสอน ประกอบด้วย:
- เนื้อหาบทเรียน
- กิจกรรม (Activities)
- ทรัพยากร (Resources)
- การประเมินผล

**ขั้นตอน:**

1. เลือก Category ที่ต้องการ
2. คลิก **Create new course**
3. กรอกข้อมูลในแต่ละ Tab:

**Tab: General**

| Field | Example | คำอธิบาย |
|-------|---------|----------|
| Course full name | ระบุชื่อวิชา | ชื่อเต็มของรายวิชา |
| Course short name | ชื่อย่อวิชา | ชื่อย่อ (แสดงใน breadcrumb) |
| Course category | วิทยาศาสตร์และเทคโนโลยี | หมวดหมู่ |
| Course start date | ระบุวันที่เริ่ม | วันที่เริ่มเรียน |
| Course end date | ระบุวันสิ้นสุด| วันที่สิ้นสุด |
| Course ID number | ระบุรหัสวิชา | รหัสวิชา (ไม่ซ้ำ) |

**Tab: Description**

```
Course summary:
 ระบุรายละเอียด หรือสังเขปรายวิชา

Learning outcomes:
1. ระบุผลการเรียนรู้ที่คาดหวังของรายวิชา
2. ระบุผลการเรียนรู้ที่คาดหวังของรายวิชา
3. ระบุผลการเรียนรู้ที่คาดหวังของรายวิชา
```

**Tab: Course format**

| Field | Value | คำอธิบาย |
|-------|-------|----------|
| Format | Topics format | แบ่งเป็นหัวข้อ (แนะนำ) |
| Number of sections | 3 | จำนวนหัวข้อ |
| Hidden sections | Hidden sections are shown in collapsed form | ซ่อนแต่แสดงชื่อ |

**Format อื่นๆ:**
- **Weekly format:** แบ่งตามสัปดาห์
- **Social format:** เหมาะกับ Forum/Discussion

**Tab: Appearance**

| Field | Value |
|-------|-------|
| Force language | - (ไม่บังคับ) |
| Number of announcements | 5 |
| Show gradebook to students | Yes |
| Show activity reports | Yes |

**Tab: Files and uploads**

| Field | Value |
|-------|-------|
| Maximum upload size | 20MB |

4. คลิก **Save and display**

**บันทึกผลการทดลอง 8:**


<img width="1912" height="1035" alt="image" src="https://github.com/user-attachments/assets/7e720df0-d77b-4092-baf7-4bfec15f54e6" />



---

### 6.4 การจัดการผู้ใช้งาน (User Management)

#### 6.4.1 ระบบผู้ใช้งานใน Moodle

**Roles (บทบาท) ใน Moodle:**

| Role | Permissions | Use Case |
|------|-------------|----------|
| Administrator | ทุกอย่าง | ผู้ดูแลระบบ |
| Manager | จัดการ Course, User | หัวหน้าแผนก |
| Course creator | สร้าง Course | อาจารย์ |
| Teacher | จัดการ Course ที่สอน | ผู้สอน |
| Non-editing teacher | ดูและประเมินผล | ผู้ช่วยสอน |
| Student | เข้าถึงเนื้อหา ส่งงาน | นักเรียน |
| Guest | ดูได้อย่างเดียว | ผู้เยี่ยมชม |

#### 6.4.2 สร้างผู้ใช้งานใหม่

**วิธีที่ 1: Manual Account (สร้างเอง)**

1. ไปที่ **Site administration → Users → Accounts → Add a new user**

2. กรอกข้อมูล:

**General:**
| Field | Example | Required |
|-------|---------|----------|
| Username | student001 | ✅ |
| Choose an authentication method | Manual accounts | ✅ |
| Password | Student@123 | ✅ |
| Force password change | No | - |

**User details:**
| Field | Example |
|-------|---------|
| First name | สมชาย |
| Surname | ใจดี |
| Email address | student001@example.com |
| Email display | Allow everyone to see |
| Email activated | This email address is enabled |

**Additional names:**
| Field | Example |
|-------|---------|
| City/town | Bangkok |
| Country | Thailand |

**Interests:**
```
Programming, Python, Data Science
```

3. คลิก **Create user**

**บันทึกผลการทดลอง 9:**

<img width="1915" height="1036" alt="image" src="https://github.com/user-attachments/assets/ce3546f7-fdda-4d60-a523-f8dcc7f1fbc6" />


**วิธีที่ 2: Upload Users (CSV)**

1. ไปที่ **Site administration → Users → Accounts → Upload users**

2. สร้างไฟล์ CSV ที่มีจำนวนนักเรียน 3 คนและครู 3 คน:

```csv
username,password,firstname,lastname,email,city,country
student001,Pass@123,สมชาย,ใจดี,student001@example.com,Bangkok,TH
student002,Pass@123,สมหญิง,รักเรียน,student002@example.com,Bangkok,TH
student003,Pass@123,สมศักดิ์,ขยัน,student003@example.com,Bangkok,TH
```

3. อัพโหลดไฟล์
4. กำหนด Upload type: **Add new only**
5. คลิก **Upload this file**
6. Preview และยืนยัน

**บันทึกผลการทดลอง 10:**


<img width="1915" height="1037" alt="image" src="https://github.com/user-attachments/assets/2fd8a144-3a31-4884-b013-d48f233804a2" />


#### 6.4.3 การลงทะเบียนเรียน (Enrolment)

**วิธีที่ 1: Manual Enrolment**

1. เปิดรายวิชาที่ต้องการ (กรณีที่ยังไม่มีรายวิชาให้ทดลองสร้างรายวิชาใหม่)
2. คลิก **Participants**
3. คลิก **Enrol users**
4. ค้นหาผู้ใช้ที่ต้องการ
5. เลือก Role: **Student**
6. คลิก **Enrol**

**วิธีที่ 2: Self Enrolment**

เปิดให้นักเรียนลงทะเบียนเอง:

1. เข้า Course
2. ไปที่ **⚙️ → More → Users → Enrolment methods**
3. เปิด **Self enrolment (Student)**
4. ตั้งค่า:

| Field | Value |
|-------|-------|
| Custom instance name | Self enrolment |
| Allow self enrolments | Yes |
| Enrolment key | (ถ้าต้องการรหัส) |
| Use group enrolment keys | No |

5. คลิก **Save changes**

นักเรียนสามารถลงทะเบียนเองโดย:
1. เข้า Course
2. คลิก **Enrol me**
3. ใส่ Enrolment key (ถ้ามี)

**วิธีที่ 3: Cohort Enrolment**

Cohort = กลุ่มผู้ใช้ (เช่น ม.1, ม.2 หรือ ,ม.1/1  ม.1/2)

1. สร้าง Cohort:
   - Site administration → Users → Accounts → Cohorts
   - Add new cohort
   - ตั้งชื่อ: "ม.1 แผนการเรียนวิทยาศาสตร์-คณิตศาสตร์"

2. เพิ่มสมาชิกใน Cohort:
   - คลิก Cohort
   - Assign users
   - เลือกผู้ใช้

3. Enrol Cohort ในรายวิชา:
   - เข้า Course
   - Enrolment methods
   - เปิด Cohort sync
   - เลือก Cohort

4. ทดสอบการ Upload Users อีกครั้ง โดยการเพิ่มคอลัมน์ cohort ในไฟล์ CSV และเพิ่มข้อมูลนักเรียนใหม่เพิ่มเติมจากไฟล์เดิม 5 คน พร้อมทั้งกำหนดค่า cohort เพิ่มเติมจากที่มีอยู่เดิม แล้วทดสอบ Upload Users
5. 
   **บันทึกผลการทดลอง 11:**

<img width="1919" height="993" alt="image" src="https://github.com/user-attachments/assets/b85ef856-0453-451e-8a0b-15c55028b08d" />

---


## 8. ขั้นตอนที่ 6: การจัดการ Container

### 8.1 คำสั่งพื้นฐาน

```bash
# ดูสถานะ
docker-compose ps

# ดู Logs
docker-compose logs -f

# หยุด Containers
docker-compose stop

# เริ่ม Containers
docker-compose start

# รีสตาร์ท
docker-compose restart

# หยุดและลบ Containers (เก็บ Volumes)
docker-compose down

# หยุดและลบทั้งหมด (รวม Volumes) ⚠️
docker-compose down -v
```

### 8.2 การเข้าไปใน Container

```bash
# เข้า Moodle container
docker exec -it moodle_app bash

# เข้า Database container
docker exec -it moodle_db bash

# เชื่อมต่อ MySQL
docker exec -it moodle_db mysql -u moodleuser -p
```

### 8.3 การสำรองข้อมูล

**Backup Database:**
```bash
docker exec moodle_db mysqldump -u moodleuser -pmoodlepassword moodle > backup_$(date +%Y%m%d).sql
```

**Backup Moodledata:**
```bash
docker cp moodle_app:/moodledata ./moodledata_backup
```

**Restore Database:**
```bash
docker exec -i moodle_db mysql -u moodleuser -pmoodlepassword moodle < backup_20240101.sql
```

---

## 10. คำถามท้ายใบงาน

### คำถามส่วนที่ 1: ความเข้าใจพื้นฐาน

**1. อธิบายความแตกต่างระหว่าง Docker Image และ Docker Container พร้อมยกตัวอย่าง**

คำตอบ:
```
Docker Image คือไฟล์ต้นแบบที่ใช้สำหรับสร้าง Docker Container ภายในประกอบด้วยระบบปฏิบัติการ โปรแกรม และการตั้งค่าต่าง ๆ แต่ยังไม่สามารถทำงานได้

Docker Container คือสิ่งที่ถูกสร้างจาก Docker Image และกำลังทำงานจริง สามารถเริ่ม หยุด และใช้งานแอปพลิเคชันได้
```

**2. จากสถาปัตยกรรมในการทดลอง มี Container กี่ตัว? แต่ละตัวมีหน้าที่อะไร?**

คำตอบ:
```
จากสถาปัตยกรรมในการทดลอง มี Container ทั้งหมด 2 ตัว

Moodle Container (Web/App Container)
ทำหน้าที่รันระบบ Moodle ให้บริการเว็บไซต์แก่ผู้ใช้ ผ่านเว็บเบราว์เซอร์ โดยใช้ Apache และ PHP

Database Container (MariaDB/MySQL Container)
ทำหน้าที่จัดเก็บและจัดการฐานข้อมูลของ Moodle เช่น ข้อมูลผู้ใช้ รายวิชา และผลการเรียน
```

**3. จากการทดลองมีการจัดการ Volume แบบใด มีข้อดีข้อเสียอย่างไร?**

คำตอบ:
```
ข้อดี:
ช่วยเก็บข้อมูลถาวร เช่น ข้อมูล Moodle และฐานข้อมูล แม้ลบหรือรีสตาร์ท Container ข้อมูลยังไม่หาย และสะดวกต่อการจัดการ

ข้อเสีย:
ไม่เห็นตำแหน่งไฟล์ชัดเจนบนเครื่องโฮสต์ และการสำรองหรือย้ายข้อมูลต้องใช้คำสั่ง Docker เพิ่มเติม
```

**4. Network ใน Docker Compose ทำหน้าที่อะไร? Container สื่อสารกันอย่างไร?**

คำตอบ:
```
Network ใน Docker Compose ทำหน้าที่เชื่อมต่อ Container ต่าง ๆ ให้อยู่ในเครือข่ายเดียวกัน เพื่อให้สามารถสื่อสารกันได้

การสื่อสารระหว่าง Container:
Container จะติดต่อกันผ่าน ชื่อ service ที่กำหนดใน docker-compose.yml แทนการใช้ IP address เช่น Moodle ติดต่อฐานข้อมูลผ่านชื่อ

```


**5. `depends_on` ในไฟล์ docker-compose.yml มีความสำคัญอย่างไร?**

คำตอบ:
```
depends_on ในไฟล์ docker-compose.yml มีความสำคัญในการกำหนด ลำดับการเริ่มทำงานของ Container โดยบอกว่า Container หนึ่งต้องเริ่มหลังอีก Container หนึ่ง
```

**6. ถ้าต้องการเปลี่ยน Port ของ Moodle  เป็น 9000 ต้องแก้ไขส่วนใดของไฟล์?**

คำตอบ:
```
หากต้องการเปลี่ยน Port ของ Moodle เป็น 9000
ต้องแก้ไขที่ส่วน ports ของ service Moodle ในไฟล์ docker-compose.yml

```

**7. Environment Variables `MOODLE_DB_HOST=db` หมายความว่าอย่างไร? ทำไมไม่ใช้ `localhost`?**

คำตอบ:
```
MOODLE_DB_HOST=db หมายถึง กำหนดให้ Moodle เชื่อมต่อฐานข้อมูลผ่าน ชื่อ service db ซึ่งเป็นชื่อของ Database Container ใน Docker Compose

เหตุผลที่ไม่ใช้ localhost
เพราะ localhost ภายใน Container หมายถึง ตัว Container เอง ไม่ใช่ Database Container จึงไม่สามารถเชื่อมต่อฐานข้อมูลได้
```


**8. เปรียบเทียบข้อดีและข้อเสียของการติดตั้ง Moodle ด้วย Docker เทียบกับการติดตั้งแบบปกติ**

คำตอบ:
```
ติดตั้ง Moodle ด้วย Docker
ข้อดี:
ติดตั้งง่าย รวดเร็ว สภาพแวดล้อมเหมือนกันทุกเครื่อง ลดปัญหาความไม่เข้ากันของระบบ และลบ–ย้ายระบบได้สะดวก

ข้อเสีย:
ต้องมีความรู้พื้นฐาน Docker และการตั้งค่าบางอย่างอาจซับซ้อนสำหรับผู้เริ่มต้น

ติดตั้ง Moodle แบบปกติ
ข้อดี:
เข้าใจการทำงานของระบบจริงได้ชัด ควบคุมการตั้งค่าเซิร์ฟเวอร์ได้ละเอียด และไม่ต้องเรียนรู้ Docker

ข้อเสีย:
ติดตั้งยุ่งยาก ใช้เวลานาน และอาจเกิดปัญหาความเข้ากันของเวอร์ชันโปรแกรมหรือระบบปฏิบัติการ
```

**9. ถ้าต้องการเพิ่ม Container Redis สำหรับ Caching จะต้องแก้ไข docker-compose.yml อย่างไร?**

คำตอบ (เขียน YAML):
```yaml

1.เพิ่ม service Redis
redis:
  image: redis:latest
  container_name: redis

2.กำหนดให้ Moodle เชื่อมต่อ Redis (เพิ่มใน service Moodle)
environment:
  MOODLE_REDIS_HOST: redis


```


**10. ถ้า Container moodle_app ไม่สามารถเชื่อมต่อ Database ได้ จะตรวจสอบและแก้ไขอย่างไร?**

คำตอบ:
```
วิธีตรวจสอบ:
ตรวจสอบสถานะ Container ฐานข้อมูล
วิธีแก้ไข:
แก้ไขและรีสตาร์ท Container

docker-compose down
docker-compose up -d
```

**11. ถ้ารัน `docker-compose down -v` จะเกิดอะไรขึ้นกับข้อมูล?**

คำตอบ:
```
ข้อมูลที่เก็บอยู่ใน Volume เช่น ฐานข้อมูล Moodle และไฟล์ผู้ใช้ จะถูกลบถาวร ไม่สามารถกู้คืนได้

```

---

## ภาคผนวก

### ภาคผนวก ก: คำสั่ง Docker ที่เป็นประโยชน์

| คำสั่ง | คำอธิบาย |
|--------|----------|
| `docker-compose up -d` | สร้างและเริ่มรัน Container ทั้งหมดในพื้นหลัง |
| `docker-compose ps` | แสดงสถานะของ Container |
| `docker-compose logs -f` | ดู Log แบบ Real-time |
| `docker-compose logs <service>` | ดู Log ของ Service เฉพาะ |
| `docker-compose stop` | หยุด Container ทั้งหมด |
| `docker-compose start` | เริ่ม Container ที่หยุดไว้ |
| `docker-compose restart` | รีสตาร์ท Container |
| `docker-compose down` | หยุดและลบ Container (เก็บ Volume) |
| `docker-compose down -v` | หยุดและลบ Container พร้อม Volume |
| `docker-compose exec <service> <command>` | รันคำสั่งใน Container |
| `docker-compose pull` | ดาวน์โหลด Image ล่าสุด |
| `docker-compose build` | Build Image ใหม่ |
| `docker volume ls` | แสดง Volume ทั้งหมด |
| `docker volume inspect <volume>` | ดูรายละเอียด Volume |
| `docker network ls` | แสดง Network ทั้งหมด |
| `docker network inspect <network>` | ดูรายละเอียด Network |

### ภาคผนวก ข: การแก้ปัญหาทั่วไป

#### ปัญหา: Container ไม่สามารถเริ่มต้นได้

**อาการ:**
```
Error: Cannot start service moodle: driver failed...
```

**การแก้ไข:**
1. ตรวจสอบ Port ว่าถูกใช้งานอยู่หรือไม่ (กรณีใช้ port 8080)
   ```bash
   # Windows
   netstat -ano | findstr :8080
   
   # macOS/Linux
   lsof -i :8080
   ```
2. เปลี่ยน Port ใน docker-compose.yml
3. รีสตาร์ท Docker Desktop

#### ปัญหา: ไม่สามารถเข้าถึง localhost:8080

**อาการ:**
- Browser แสดง "This site can't be reached"

**การแก้ไข:**
1. ตรวจสอบว่า Container กำลังรันอยู่
   ```bash
   docker-compose ps
   ```
2. ดู Logs
   ```bash
   docker-compose logs moodle
   ```
3. รอให้ Apache เริ่มต้นเสร็จ (2-3 นาที)
4. ลอง http://127.0.0.1:8080

#### ปัญหา: Database connection error

**อาการ:**
```
Error connecting to database
```

**การแก้ไข:**
1. ตรวจสอบว่า db container รันอยู่
   ```bash
   docker-compose ps
   ```
2. ตรวจสอบ Environment Variables
3. รีสตาร์ท Containers
   ```bash
   docker-compose restart
   ```
4. ตรวจสอบ Network
   ```bash
   docker network inspect moodle-docker_moodle_network
   ```

#### ปัญหา: Permission denied

**อาการ:**
```
Permission denied: /moodledata
```

**การแก้ไข:**
```bash
# เข้า Container
docker exec -it moodle_app bash

# แก้ไข Permission
chown -R www-data:www-data /moodledata
chmod -R 755 /moodledata
```


###  เอกสารเพิ่มเติม

**Official Documentation:**
- Moodle Docs: https://docs.moodle.org
- Docker Docs: https://docs.docker.com
- Docker Compose: https://docs.docker.com/compose

**Docker Images:**
- lthub/moodle: https://hub.docker.com/r/lthub/moodle
- MariaDB: https://hub.docker.com/_/mariadb

**Community:**
- Moodle Forums: https://moodle.org/forums
- Docker Community: https://forums.docker.com

**Tutorials:**
- Moodle Administration: https://docs.moodle.org/en/Admin_quick_guide
- Docker for Beginners: https://docker-curriculum.com

---
