บันทึกผลการทดลอง 1:
![alt text](image.png)
บันทึกผลการทดลอง 2:
![alt text](image-1.png)
บันทึกผลการทดลอง 3:
![alt text](image-2.png)
บันทึกผลการทดลอง 4:
![alt text](image-3.png)
บันทึกผลการทดลอง 6:
![alt text](image-4.png)
บันทึกผลการทดลอง 6-1:
![alt text](image-5.png)
บันทึกผลการทดลอง 7:
![alt text](image-6.png)
บันทึกผลการทดลอง 8:
![alt text](image-7.png)
บันทึกผลการทดลอง 9:
![alt text](image-8.png)
บันทึกผลการทดลอง 10:
![alt text](image-9.png)
บันทึกผลการทดลอง 11:
![alt text](image-11.png)

10. คำถามท้ายใบงาน

1. อธิบายความแตกต่างระหว่าง Docker Image และ Docker Container พร้อมยกตัวอย่าง

คำตอบ: Docker Image คือไฟล์แม่แบบที่ใช้สำหรับสร้าง Docker Container ภายในประกอบด้วยระบบปฏิบัติการ โปรแกรม ไลบรารี และการตั้งค่าที่จำเป็น โดย Image ยังไม่สามารถทำงานได้ด้วยตัวเอง
Docker Container คืออินสแตนซ์ที่ถูกสร้างจาก Docker Image และอยู่ในสถานะพร้อมทำงาน สามารถเริ่ม หยุด และใช้งานแอปพลิเคชันได้จริง
ตัวอย่าง
Image เช่น mariadb:latest เมื่อนำมารันจะเกิดเป็น Container สำหรับใช้งานฐานข้อมูล MariaDB ในระบบ

2. จากสถาปัตยกรรมในการทดลอง มี Container กี่ตัว? แต่ละตัวมีหน้าที่อะไร?
คำตอบ: จากสถาปัตยกรรมในการทดลอง มี Container ทั้งหมด 2 ตัว ได้แก่
Database Container (MariaDB)
ทำหน้าที่จัดเก็บและจัดการฐานข้อมูลของระบบ Moodle เช่น ข้อมูลผู้ใช้ รายวิชา คะแนน และกิจกรรมต่าง ๆ
Application Container (Moodle)
ทำหน้าที่เป็นเว็บแอปพลิเคชันสำหรับให้ผู้ใช้งานเข้าถึงระบบ Moodle ผ่านเว็บเบราว์เซอร์ โดยเชื่อมต่อกับฐานข้อมูล MariaDB เพื่อแสดงผลและจัดการข้อมูลการเรียนการสอน

3. จากการทดลองมีการจัดการ Volume แบบใด มีข้อดีข้อเสียอย่างไร?
คำตอบ: จากการทดลอง มีการจัดการ Volume แบบ Docker Named Volume
โดยมีการสร้าง Volume แยกต่างหากสำหรับจัดเก็บข้อมูลของฐานข้อมูลและข้อมูลของระบบ Moodle เช่น db_data และ moodledata
ข้อดี
ข้อมูลไม่สูญหายเมื่อ Container ถูกลบหรือสร้างใหม่
แยกข้อมูลออกจาก Container ทำให้ง่ายต่อการจัดการและสำรองข้อมูล
เหมาะสำหรับระบบที่ต้องเก็บข้อมูลถาวร เช่น ฐานข้อมูลและไฟล์ผู้ใช้งาน
ข้อเสีย
ต้องมีการจัดการ Volume เพิ่มเติม เช่น การลบหรือสำรองข้อมูล
หากลืมลบ Volume อาจทำให้ใช้พื้นที่จัดเก็บมากขึ้นโดยไม่จำเป็น

4. Network ใน Docker Compose ทำหน้าที่อะไร? Container สื่อสารกันอย่างไร?

คำตอบ: Network ใน Docker Compose ทำหน้าที่เชื่อมต่อ Container ต่าง ๆ ให้สามารถสื่อสารกันได้ภายในเครือข่ายเดียวกันอย่างปลอดภัย โดยแยกออกจากเครือข่ายภายนอก
การสื่อสารของ Container
Container จะสื่อสารกันผ่านชื่อ Service ที่กำหนดในไฟล์ docker-compose.yml เช่น Moodle สามารถเชื่อมต่อฐานข้อมูลได้โดยใช้ชื่อ db แทนการใช้ IP Address ทำให้ระบบมีความยืดหยุ่นและจัดการได้ง่ายขึ้น

5. depends_on ในไฟล์ docker-compose.yml มีความสำคัญอย่างไร?

คำตอบ: depends_on ในไฟล์ docker-compose.yml มีความสำคัญในการกำหนดลำดับการเริ่มต้นของ Container โดยระบุว่า Container ใดต้องเริ่มทำงานก่อน เพื่อให้ Container ที่พึ่งพาสามารถเริ่มทำงานได้อย่างถูกต้อง เช่น กำหนดให้ Container ของแอปพลิเคชันเริ่มทำงานหลังจาก Container ฐานข้อมูลถูกสร้างและเริ่มต้นแล้ว อย่างไรก็ตาม depends_on ไม่ได้ตรวจสอบความพร้อมในการใช้งานของบริการภายใน Container เพียงแค่ควบคุมลำดับการเริ่มต้นเท่านั้น

6. ถ้าต้องการเปลี่ยน Port ของ Moodle เป็น 9000 ต้องแก้ไขส่วนใดของไฟล์?

คำตอบ: หากต้องการเปลี่ยน Port ของ Moodle เป็น 9000 ต้องแก้ไขที่ส่วน ports ของ service moodle ในไฟล์ docker-compose.yml โดยเปลี่ยนค่าด้านซ้าย (ฝั่งเครื่อง host)

7. Environment Variables MOODLE_DB_HOST=db หมายความว่าอย่างไร? ทำไมไม่ใช้ localhost?

คำตอบ: ตัวแปร MOODLE_DB_HOST=db หมายความว่า ระบบ Moodle จะเชื่อมต่อไปยังฐานข้อมูลโดยใช้ชื่อ Service db ซึ่งเป็นชื่อของ Container ฐานข้อมูลที่กำหนดไว้ในไฟล์ docker-compose.yml
เหตุผลที่ไม่ใช้ localhost เนื่องจากภายใน Docker แต่ละ Container มีสภาพแวดล้อมแยกจากกัน หากใช้ localhost จะหมายถึง Container ของ Moodle เอง ไม่ใช่ Container ของฐานข้อมูล ดังนั้นจึงต้องใช้ชื่อ Service (db) เพื่อให้ Container สามารถสื่อสารกันผ่าน Docker Network ได้อย่างถูกต้อง

8. เปรียบเทียบข้อดีและข้อเสียของการติดตั้ง Moodle ด้วย Docker เทียบกับการติดตั้งแบบปกติ

คำตอบ: ติดตั้ง Moodle ด้วย Docker
ข้อดี: ติดตั้งง่าย รวดเร็ว สภาพแวดล้อมเหมือนกันทุกเครื่อง
ข้อเสีย: ต้องมีความรู้ Docker และปรับแต่งลึกได้จำกัด
ติดตั้ง Moodle แบบปกติ
ข้อดี: ปรับแต่งระบบได้ละเอียด
ข้อเสีย: ติดตั้งยาก ใช้เวลานาน และเสี่ยงปัญหาเวอร์ชัน

9. ถ้าต้องการเพิ่ม Container Redis สำหรับ Caching จะต้องแก้ไข docker-compose.yml อย่างไร?

คำตอบ (เขียน YAML): เพิ่ม service redis สำหรับทำหน้าที่ Caching และกำหนดให้ Moodle เชื่อมต่อ Redis ผ่านชื่อ service redis บน Docker Network เดียวกัน services:
  db:
    image: mariadb:latest
    container_name: moodle_db
    environment:
      MYSQL_ROOT_PASSWORD: 1234
      MYSQL_DATABASE: moodle
      MYSQL_USER: moodleuser
      MYSQL_PASSWORD: 1234
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - moodle_network

  redis:
    image: redis:latest
    container_name: moodle_redis
    networks:
      - moodle_network

  moodle:
    image: lthub/moodle:education-4.5.8
    container_name: moodle_app
    ports:
      - "8080:80"
    environment:
      MOODLE_DB_HOST: db
      MOODLE_DB_NAME: moodle
      MOODLE_DB_USER: moodleuser
      MOODLE_DB_PASSWORD: 1234
      MOODLE_REDIS_HOST: redis
    volumes:
      - moodledata:/moodledata
    depends_on:
      - db
      - redis
    networks:
      - moodle_network

networks:
  moodle_network:
    driver: bridge

volumes:
  db_data:
  moodledata:

10. ถ้า Container moodle_app ไม่สามารถเชื่อมต่อ Database ได้ จะตรวจสอบและแก้ไขอย่างไร?

คำตอบ: วิธีตรวจสอบ: 
ตรวจสอบว่า Container ฐานข้อมูล (moodle_db) อยู่ในสถานะทำงาน (docker ps)
ตรวจสอบค่าการเชื่อมต่อฐานข้อมูลในไฟล์ docker-compose.yml เช่น MOODLE_DB_HOST, MOODLE_DB_USER, และ MOODLE_DB_PASSWORD
ตรวจสอบ Network ว่า Container ทั้งสองอยู่ในเครือข่ายเดียวกัน
ตรวจสอบ Log ของ Container (docker logs moodle_app และ docker logs moodle_db) เพื่อหาสาเหตุของปัญหา
รีสตาร์ท Container หรือรัน docker compose up -d ใหม่หลังแก้ไขการตั้งค่า
วิธีแก้ไข: แก้ไขค่าการเชื่อมต่อฐานข้อมูลในไฟล์ docker-compose.yml ให้ถูกต้อง เช่น MOODLE_DB_HOST=db, MOODLE_DB_USER, และ MOODLE_DB_PASSWORD
ตรวจสอบให้ Container ฐานข้อมูลเริ่มทำงานก่อน โดยใช้ depends_on
หากข้อมูลฐานข้อมูลเสียหาย ให้ลบ Volume แล้วสร้างใหม่
หลังแก้ไขให้รัน docker compose down และ docker compose up -d เพื่อให้การตั้งค่าใหม่มีผล

11. ถ้ารัน docker-compose down -v จะเกิดอะไรขึ้นกับข้อมูล?

คำตอบ คำสั่ง docker-compose down -v จะหยุดและลบ Container พร้อมทั้งลบ Volume ทั้งหมด ที่ผูกกับบริการนั้น ส่งผลให้ข้อมูลที่เก็บอยู่ใน Volume เช่น ฐานข้อมูลและไฟล์ของระบบ Moodle ถูกลบถาวรและไม่สามารถกู้คืนได้











