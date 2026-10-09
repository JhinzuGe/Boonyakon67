[readme.md](https://github.com/user-attachments/files/33239065/readme.md)
# 🚀 CAI WebAR Courseware: เทคโนโลยีความจริงเสริม (AR)
### รายวิชาวิทยาการคำนวณ (ว25101) ชั้นประถมศึกษาปีที่ 5 โรงเรียนเต่างอยพัฒนะศึกษา

บทเรียนคอมพิวเตอร์ช่วยสอน (Computer-Assisted Instruction: CAI) บนเว็บเบราว์เซอร์ ผสานเทคโนโลยีความจริงเสริม (WebAR) และระบบหลังบ้านด้วย Supabase รองรับการเรียนรู้แบบปฏิสัมพันธ์ แบบทดสอบคู่ขนานอัตโนมัติ และระบบตรวจงาน/ประเมินผลการเรียนรู้

---

## 📌 คุณลักษณะสำคัญของระบบ (Key Features)

1. **การยืนยันตัวตนและการจัดการสิทธิ์ (Authentication & RLS):**
   - นักเรียนสมัครเข้าเรียนด้วยรหัสนักเรียน 11 หลัก (เช่น `66010123401`) ระบบจะผูกบัญชีเข้ากับรายชื่อในตาราง `students` อัตโนมัติผ่าน Postgres Trigger
   - อาจารย์เข้าสู่ระบบด้วยอีเมลโรงเรียน (เช่น `teacher@taongoy.ac.th`)
   - ปกป้องข้อมูลด้วย Row Level Security (RLS) แยกสิทธิ์ผู้เรียนและผู้สอนชัดเจน

2. **ระบบสลับชุดข้อสอบคู่ขนานอัตโนมัติ (Parallel Exam System):**
   - จัดสรรชุดข้อสอบ A/B อัตโนมัติด้วยเลขท้ายรหัสนักเรียน (เลขคี่: Pre=ชุด A / Post=ชุด B, เลขคู่: Pre=ชุด B / Post=ชุด A)
   - คำนวณดัชนีประสิทธิผล **Normalized Gain ($g$)** ตามแนวคิดของ Hake (1998)

3. **สื่อปฏิสัมพันธ์ WebAR 3 มิติ:**
   - แสดงผลโมเดล 3D แบบ Interactive ด้วย `<model-viewer>` v3.4.0
   - รองรับการเปิดกล้องแสดงผลความจริงเสริม (Augmented Reality) ผ่านสมาร์ตโฟนโดยไม่ต้องติดตั้งแอปพลิเคชันเพิ่ม

4. **การส่งงานปฏิบัติและย่อขนาดรูปภาพอัตโนมัติ:**
   - ผู้เรียนสามารถแนบลิงก์ หรืออัปโหลดไฟล์งาน (PNG, JPG, PDF, MP4) เข้าสู่ Private Storage Bucket
   - ย่อขนาดรูปโปรไฟล์ฝั่ง Client (HTML Canvas) ไม่เกิน $400 \times 400$ px ก่อนอัปโหลดเพื่อประหยัดพื้นที่จัดเก็บ

5. **แดชบอร์ดอาจารย์ผู้สอน (Teacher Dashboard):**
   - ดูสรุปความก้าวหน้า และคะแนน Normalized Gain ของผู้เรียนรายบุคคล
   - ส่งออกข้อมูลผลการเรียนเป็นไฟล์ CSV (UTF-8 with BOM รองรับการเปิดอ่านภาษาไทยใน Microsoft Excel)
   - ระบบให้คะแนนและข้อเสนอแนะงานปฏิบัติของผู้เรียน
   - ระบบอัปโหลดและบริหารจัดการสื่อการเรียนรู้เพิ่มเติม (PPTX, DOCX, PDF, ลิงก์วิดีโอ)

---

## 📁 โครงสร้างไฟล์ในโครงการ (Project Structure)

```text
.
├── courseware.html      # แอปพลิเคชันเว็บแบบ Single-File (HTML + Tailwind CSS + JS + WebAR)
├── setup.sql            # สคริปต์ SQL ตั้งค่าตาราง RLS Trigger View และ Storage บน Supabase
├── documentation.md     # เอกสารประกอบการใช้งาน ข้อสมมติ System Test Matrix และคู่มือติดตั้ง
└── README.md            # เอกสารอธิบายภาพรวมโครงการและการเริ่มต้นใช้งาน
```

---

## 🛠️ เทคโนโลยีที่ใช้ (Tech Stack)

- **Frontend:** HTML5, Tailwind CSS (CDN), JavaScript (ES6 Modules)
- **3D & AR Rendering:** Google `<model-viewer>` v3.4.0 (WebXR / Scene Viewer / Quick Look)
- **Backend & Database:** [Supabase](https://supabase.com/) (PostgreSQL, GoTrue Auth, Realtime Storage)
- **Font:** Google Fonts (Sarabun)

---

## 🗄️ โครงสร้างฐานข้อมูล (Database Schema)

ระบบประกอบด้วย 6 ตารางหลัก 1 View และ 3 Private Storage Buckets:

| ตาราง / View | หน้าที่การทำงาน |
| :--- | :--- |
| `teachers` | เก็บบัญชีรายชื่อผู้สอนที่ได้รับสิทธิ์ดูแลระบบ |
| `students` | เก็บรายชื่อนักเรียน 11 หลัก และรหัสนักเรียนสำหรับผูกบัญชี |
| `attempts` | บันทึกผลการทำแบบทดสอบก่อนเรียน (Pre-test) และหลังเรียน (Post-test) |
| `progress` | บันทึกความก้าวหน้าการเข้าเรียนและการตอบคำถามแทรก 5 หน่วย |
| `submissions` | บันทึกงานปฏิบัติ ลิงก์/ไฟล์แนบ คะแนน และคำติชมจากอาจารย์ |
| `materials` | คลังเก็บสื่อการเรียนรู้เพิ่มเติมที่อัปโหลดโดยอาจารย์ผู้สอน |
| **`results` (View)** | สรุปผลคะแนน Pre/Post, ความก้าวหน้าสูงสุด และคำนวณ **Normalized Gain** |

### Storage Buckets (Private)
1. `photos` (≤ 2MB) – รูปโปรไฟล์นักเรียน
2. `submissions` (≤ 20MB) – ไฟล์งานปฏิบัติของนักเรียน
3. `materials` (≤ 50MB) – ไฟล์สื่อการเรียนรู้เพิ่มเติมจากอาจารย์

---

## 🚀 ขั้นตอนการติดตั้งและการใช้งาน (Quick Start Guide)

### 1. ตั้งค่า Supabase Backend
1. สมัครใช้งานและสร้างโปรเจกต์ใหม่บน [Supabase](https://supabase.com) (แนะนำภูมิภาค Singapore)
2. ไปที่ **Authentication > Providers > Email** ปิดตัวเลือก **Confirm email** แล้วกด Save
3. ไปที่ **SQL Editor** คัดลอกโค้ดทั้งหมดจากไฟล์ `setup.sql` วางแล้วกด **Run**
4. เพิ่มบัญชีอาจารย์: สมัครผู้ใช้ใหม่ในเมนู **Authentication** แล้วรันคำสั่งเพิ่ม `user_id` ลงในตาราง `teachers`:
   ```sql
   INSERT INTO public.teachers (user_id, full_name)
   VALUES ('YOUR_TEACHER_USER_ID', 'อ.ผู้สอน เต่างอย');
   ```

### 2. ตั้งค่าไฟล์ Frontend (`courseware.html`)
1. คัดลอก `Project URL` และ `anon / public key` จากหน้า **Project Settings > API** บน Supabase
2. เปิดไฟล์ `courseware.html` แก้ไขบรรทัดสคริปต์ส่วนต้น:
   ```javascript
   const SUPABASE_URL = 'https://your-project.supabase.co';
   const SUPABASE_ANON_KEY = 'your-anon-key-here';
   ```

### 3. เริ่มใช้งาน (Deployment)
- เปิดไฟล์ `courseware.html` บนเว็บเบราว์เซอร์ได้ทันที
- หรืออัปโหลดไฟล์ `courseware.html` ขึ้นโฮสติ้งฟรี เช่น **GitHub Pages**, **Netlify** หรือ **Vercel** เพื่อให้นักเรียนเข้าเรียนผ่านสมาร์ตโฟนได้ทุกที่

---

## 🧪 บัญชีสำหรับทดสอบระบบ (Test Credentials)

นักเรียนสมมติถูกเตรียมไว้ในสคริปต์ `setup.sql` สามารถลงทะเบียนด้วยรหัสนักเรียน 11 หลักดังนี้:

| รหัสนักเรียน | ชื่อ-นามสกุล | ชุดข้อสอบที่ได้ |
| :--- | :--- | :--- |
| `66010123401` | เด็กชายก้องภพ ใจดี | Pre: ชุด A / Post: ชุด B |
| `66010123402` | เด็กหญิงมณีรัตน์ แจ่มใส | Pre: ชุด B / Post: ชุด A |
| `66010123403` | เด็กชายปัญญาวุฒิ เรียนดี | Pre: ชุด A / Post: ชุด B |

---

## 📐 การคำนวณ Normalized Gain ($g$)

สูตรคำนวณดัชนีประสิทธิผลตาม Hake (1998):
$$g = \frac{\text{PostScore} - \text{PreScore}}{\text{FullScore} - \text{PreScore}}$$

*กรณีพิเศษ:* หากนักเรียนทำคะแนนก่อนเรียนได้เต็ม 10 ($Pre = 10$) ระบบจะกำหนดค่า $g = 1.00$ อัตโนมัติเพื่อป้องกันข้อผิดพลาดการหารด้วยศูนย์ (Division by Zero)

---

## 📄 ใบอนุญาตและการเผยแพร่ (License)

สื่อการเรียนรู้นี้จัดทำขึ้นเพื่อการศึกษา รายวิชาวิทยาการคำนวณ โรงเรียนเต่างอยพัฒนะศึกษา
```
eof
