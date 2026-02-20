# การติดตั้ง Moodle LMS ด้วย Docker Compose (lthub/moodle)

## 📚 ไฟล์ในโฟลเดอร์นี้

1. **Moodle_Docker_Lab_Complete.md** - ใบงานการทดลองฉบับสมบูรณ์ (Markdown)
   - ทฤษฎีและหลักการ
   - Architecture diagrams
   - ขั้นตอนการปฏิบัติแบบละเอียด
   - การตั้งค่า Admin ขั้นสูง
   - คำถามท้ายใบงาน

2. **docker-compose.yml** - ไฟล์ Docker Compose พร้อมใช้งาน

3. **README.md** - คู่มือการใช้งานแบบย่อ (ไฟล์นี้)

---

## ข้อมูลพื้นฐาน
- **Image**: lthub/moodle:latest
- **Database**: MariaDB
- **Port**: 8080 (HTTP)
- **Platform**: รองรับ Windows, macOS, Linux (AMD64 และ ARM64)

## ขั้นตอนการติดตั้ง

### 1. เตรียมโฟลเดอร์
```bash
mkdir moodle-docker
cd moodle-docker
```

### 2. วาง docker-compose.yml ลงในโฟลเดอร์

### 3. รัน Docker Compose
```bash
docker-compose up -d
```

### 4. ตรวจสอบสถานะ
```bash
docker-compose ps
```

### 5. ดู Log
```bash
docker-compose logs -f moodle
```

### 6. เข้าสู่หน้าติดตั้ง
เปิด browser: http://localhost:8080

## การติดตั้งผ่าน Web Interface

### ขั้นตอนที่ 1: เลือกภาษา
- เลือก English หรือภาษาที่ต้องการ
- คลิก Next

### ขั้นตอนที่ 2: ตั้งค่า Database
- Database type: **MariaDB**
- Database host: **db**
- Database name: **moodle**
- Database user: **moodleuser**
- Database password: **moodlepassword**
- คลิก Next

### ขั้นตอนที่ 3: ยืนยันข้อตกลงลิขสิทธิ์
- อ่านข้อตกลง GPL
- คลิก Continue

### ขั้นตอนที่ 4: ตรวจสอบ Server Requirements
- รอให้ระบบตรวจสอบ
- ถ้าทุกอย่างเป็นสีเขียว คลิก Continue

### ขั้นตอนที่ 5: ติดตั้งฐานข้อมูล
- รอให้ระบบติดตั้ง (2-3 นาที)
- คลิก Continue เมื่อเสร็จ

### ขั้นตอนที่ 6: สร้าง Admin Account
- Username: **admin** (ค่าเริ่มต้น)
- Password: **Admin@123** (หรือรหัสผ่านที่ปลอดภัย)
- Email: **admin@example.com**
- กรอกข้อมูลส่วนตัว
- คลิก Update profile

### ขั้นตอนที่ 7: ตั้งค่าเว็บไซต์
- Site name: **My Moodle Site**
- Short name: **Moodle**
- คลิก Save changes

## ข้อมูลการเข้าสู่ระบบ

### Admin
- URL: http://localhost:8080
- Username: admin
- Password: (ที่ตั้งไว้ในขั้นตอนที่ 6)

### Database (ภายใน Docker network)
- Host: db
- Database: moodle
- User: moodleuser
- Password: moodlepassword

## คำสั่งที่เป็นประโยชน์

```bash
# หยุด Container
docker-compose stop

# เริ่ม Container
docker-compose start

# รีสตาร์ท
docker-compose restart

# หยุดและลบ Container (เก็บ Volume)
docker-compose down

# หยุดและลบทั้ง Container และ Volume
docker-compose down -v

# ดู Log แบบ real-time
docker-compose logs -f

# เข้าไปใน Moodle container
docker exec -it moodle_app bash

# เข้าไปใน Database container
docker exec -it moodle_db mysql -u moodleuser -p
```

## การแก้ปัญหา

### ปัญหา: ไม่สามารถเข้าถึง localhost:8080 ได้
**วิธีแก้:**
- ตรวจสอบว่า Container กำลังรันอยู่: `docker-compose ps`
- ดู Log: `docker-compose logs moodle`
- ลอง http://127.0.0.1:8080
- รีสตาร์ท Container: `docker-compose restart`

### ปัญหา: Database connection error
**วิธีแก้:**
- รอให้ Database container เริ่มต้นเสร็จ (ประมาณ 30 วินาที)
- ตรวจสอบว่าใช้ hostname: **db** (ไม่ใช่ localhost)
- รีสตาร์ท: `docker-compose restart moodle`

### ปัญหา: Port 8080 ถูกใช้งานอยู่
**วิธีแก้:**
- แก้ไข docker-compose.yml เปลี่ยน port:
  ```yaml
  ports:
    - "8081:80"  # หรือ port อื่นที่ว่าง
  ```
- รีสตาร์ท: `docker-compose down && docker-compose up -d`

### ปัญหา: Windows - ช้ามาก
**วิธีแก้:**
- เปิดใช้งาน WSL 2 ใน Docker Desktop
- ปิด Antivirus ชั่วคราว
- เพิ่ม RAM ให้ Docker (Settings > Resources)

### ปัญหา: Mac M1-M4 - Image ไม่รัน
**วิธีแก้:**
- lthub/moodle รองรับ ARM64 แล้ว
- เปิด Rosetta 2 emulation (Docker Desktop > Settings > General)

## ข้อมูลเพิ่มเติม

### Volumes
- `db_data`: เก็บข้อมูล MariaDB
- `moodledata`: เก็บไฟล์ Moodle และข้อมูลผู้ใช้

### Network
- `moodle_network`: Bridge network สำหรับให้ Container สื่อสารกัน

### การสำรองข้อมูล
```bash
# Backup Database
docker exec moodle_db mysqldump -u moodleuser -pmoodlepassword moodle > backup.sql

# Backup Moodledata
docker cp moodle_app:/moodledata ./moodledata_backup

# Restore Database
docker exec -i moodle_db mysql -u moodleuser -pmoodlepassword moodle < backup.sql
```

## ข้อควรระวัง

⚠️ **การใช้งานในสภาพแวดล้อม Production:**
- เปลี่ยนรหัสผ่านทั้งหมด
- ตั้งค่า SSL/HTTPS
- ใช้ Strong password
- สำรองข้อมูลสม่ำเสำ
- อัพเดท Image เป็นประจำ
- ตั้งค่า Firewall

## แหล่งข้อมูลเพิ่มเติม

- lthub/moodle Docker Hub: https://hub.docker.com/r/lthub/moodle
- Moodle Documentation: https://docs.moodle.org
- Docker Compose Documentation: https://docs.docker.com/compose/
