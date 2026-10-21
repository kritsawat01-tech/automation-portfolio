# 📬 ระบบส่งและติดตามเอกสารอัตโนมัติผ่าน LINE (Internal Document Tracker)

> **ขอบเขตการเผยแพร่:** เอกสารนี้เป็นกรณีศึกษาสำหรับ Portfolio โดยไม่รวมซอร์สโค้ดระบบจริง ภาพหน้าจอ เอกสารภายใน หรือข้อมูลผู้ใช้งาน ตัวอย่างข้อมูลใช้เพื่ออธิบายโครงสร้างเท่านั้น รายละเอียดการทำงานอ้างอิงคำอธิบายโครงการและยังไม่ได้ตรวจยืนยันกับระบบจริงในชุดไฟล์นี้


> โซลูชันบริหารจัดการการส่งเอกสารภายในองค์กร เชื่อมต่อแบบไร้รอยต่อผ่าน LINE LIFF, Google Workspace API (Google Drive & Google Sheets) และ LINE Messaging API เพื่อการแจ้งเตือนเมื่อมีการบันทึกหรือเปลี่ยนสถานะ

---

## 🌟 ไฮไลต์ของระบบ (Project Highlights)
* **Single-Stack Backend:** สถาปัตยกรรมแบบ Serverless ทำงานบน Google Apps Script (Serverless hosting ภายใต้โควตาของบริการ)
* **Seamless User Experience:** ผู้ใช้กรอกฟอร์ม เลือกชั้นความลับ แนบรูปเอกสารผ่าน LINE LIFF ได้ทันทีจาก Rich Menu ในโทรศัพท์มือถือ
* **Automated Drive Organization:** ระบบแยกโฟลเดอร์เก็บเอกสารอัตโนมัติใน Google Drive เป็นรายบุคคล (`SenderName_UserID`) ป้องกันความสับสน
* **Dual-Role Interface:** แยกหน้าจอการทำงานระหว่างพนักงานทั่วไป (`index.html`) และแผงควบคุมของแอดมิน (`admin.html`) ผ่านระบบ URL Query Routing โดยการแยก URL ต้องมีการตรวจสอบสิทธิ์ฝั่งเซิร์ฟเวอร์ร่วมด้วย
* **Closed-loop Status Workflow:** แอดมินสามารถอัปเดตสถานะ ส่งต่อ และอัปโหลดภาพใบเสร็จ/หลักฐานการจัดส่ง เพื่อส่งกลับแจ้งเตือนพนักงานแบบ Push Message ผ่าน LINE Bot อัตโนมัติ

---

## 🛠️ Tech Stack & Architecture

| ส่วนประกอบ | เทคโนโลยี / เครื่องมือ | หน้าที่ |
|---|---|---|
| **Frontend** | HTML5, CSS3, Vanilla JavaScript, LINE LIFF SDK v2 | หน้าฟอร์มบันทึกข้อมูล, พรีวิวภาพ, แปลงไฟล์เป็น Base64 |
| **Backend** | Google Apps Script (GAS) | REST-like Routing, Image Decoding, Business Logic |
| **Database** | Google Sheets | บันทึก Log การส่งเอกสาร รหัสติดตาม และประวัติสถานะ |
| **File Storage** | Google Drive API | จัดเก็บไฟล์ภาพเอกสารต้นฉบับและภาพหลักฐานการจัดส่ง |
| **Messaging** | LINE Messaging API (Push Messages) | ส่งข้อความแจ้งเตือนสถานะจากข้อมูลที่บันทึกไว้ พร้อมแสดงผลรูปภาพ |

---

## 📐 ผังการทำงานของระบบ (System Workflow)

```
[พนักงาน (User)]
       │
       ▼
[LINE Rich Menu] ──▶ [เปิด LIFF App: index.html]
       │
       ├─ กรอกหัวข้อ / ความเร่งด่วน / หน่วยงานปลายทาง
       └─ แนบภาพเอกสาร (แปลงเป็น Base64 + แสดง Live Preview)
       │
       ▼ (ส่งข้อมูลผ่าน google.script.run)
[Google Apps Script: processFormData]
       │
       ├─ 1. สร้าง/ค้นหาโฟลเดอร์ใน Google Drive: [ผู้ส่ง_UserID]
       ├─ 2. ถอดรหัส Base64 แล้วสร้างไฟล์ภาพลงใน Drive พร้อมสิทธิ์ View
       ├─ 3. บันทึก Metadata ลงใน Google Sheets (สถานะเริ่มต้น: "ยังไม่ส่ง")
       └─ 4. ยิง Push Notification แจ้งเตือนแอดมินทันที
       │
       ▼
[แอดมิน (Admin)] ──▶ [เปิด LIFF App: admin.html]
       │
       ├─ ตรวจสอบรายการเอกสารและเปิดดูไฟล์แนบ
       ├─ ปรับสถานะเป็น "อยู่ระหว่างดำเนินการ"
       └─ กดยืนยันการจัดส่ง พร้อมอัปโหลด "ภาพหลักฐานการจัดส่ง"
       │
       ▼
[ระบบส่งการแจ้งเตือนกลับอัตโนมัติ]
       │
       └─ ส่งข้อความ Push Notification พร้อมแสดงภาพหลักฐานกลับไปยัง LINE ของผู้ส่งเดิม
```

---

## 📂 โครงสร้างระบบที่อธิบาย (System Structure)

```text
├── Code.gs             # สคริปต์หลักฝั่ง Backend จัดการ Routing, Sheets, Drive และ LINE Push API
├── index.html          # หน้าฟอร์มส่งเอกสารสำหรับพนักงาน พร้อม LIFF SDK และ Loading State
├── admin.html          # แผงควบคุม (Dashboard) สำหรับแอดมิน ตรวจสอบและอัปเดตสถานะงาน
└── README.md           # เอกสารอธิบายระบบและการติดตั้งใช้งาน
```

---

## ⚙️ การตั้งค่าและการติดตั้ง (Setup & Configuration)

> ขั้นตอนนี้อธิบายการตั้งค่าระบบต้นทาง ซอร์สโค้ดที่อ้างถึงไม่ได้รวมอยู่ในชุด Portfolio นี้ จึงยังไม่ใช่คู่มือสำหรับติดตั้งจากไฟล์ที่เผยแพร่โดยตรง

### 1. การเตรียม Google Sheets & Google Drive
1. สร้าง **Google Sheet** ใหม่ ตั้งชื่อแท็บว่า `logs` และกำหนดหัวคอลัมน์แถวแรกดังนี้:
   ```text
   A: เวลาที่บันทึก
   B: ความเร่งด่วน
   C: ชั้นความลับ
   D: หน่วยงานปลายทาง
   E: ผู้ส่ง
   F: ลิงก์ไฟล์
   G: หมายเหตุ
   H: หัวข้อ
   I: รหัสเอกสาร
   J: ไอดีผู้ส่ง
   K: สถานะ
   L: ลิงก์หลักฐาน
   ```
2. สร้างโฟลเดอร์หลักใน **Google Drive** สำหรับจัดเก็บเอกสาร แล้วคัดลอก `Folder ID` เก็บไว้

### 2. การตั้งค่า Google Apps Script
1. เปิด Google Sheet > ไปที่แท็บ **ส่วนขยาย (Extensions)** > **Apps Script**
2. สร้างและใส่โค้ดในไฟล์ `Code.gs`, `index.html`, และ `admin.html`
3. แก้ไขตัวแปรในส่วน `CONFIG` ด้านบนของ `Code.gs`:
   ```javascript
   const ADMIN_USER_ID = "YOUR_ADMIN_USER_ID"; 
   const CHANNEL_ACCESS_TOKEN = PropertiesService.getScriptProperties().getProperty('CHANNEL_ACCESS_TOKEN');
   const PARENT_DRIVE_FOLDER_ID = "YOUR_GOOGLE_DRIVE_FOLDER_ID"; 
   const SHEET_NAME = "logs";
   ```
4. กด **Deploy** > **New deployment**
   * เลือกประเภท: **Web app**
   * Execute as: **Me**
   * Who has access: จำกัดเฉพาะผู้ใช้งานที่ได้รับอนุญาตตามรูปแบบการเชื่อมต่อ พร้อมตรวจสอบตัวตนและสิทธิ์ฝั่งเซิร์ฟเวอร์
5. คัดลอก **Web app URL** ที่ได้มา

### 3. การเชื่อมต่อ LINE และ LIFF
1. เตรียมการเชื่อมต่อ LINE Messaging API สำหรับการแจ้งเตือน และ LIFF สำหรับหน้าฟอร์ม
2. กำหนด Endpoint URL ของหน้าผู้ใช้และหน้าแอดมินให้ตรงกับการติดตั้งจริง
3. กำหนด LIFF ID และ scopes ตามความสามารถที่ใช้ และตรวจสอบวิธีตั้งค่าจากเอกสารผู้ให้บริการก่อนติดตั้ง
4. ตรวจสอบตัวตนและสิทธิ์ของผู้ใช้ฝั่งเซิร์ฟเวอร์ก่อนอนุญาตให้อ่านเอกสารหรือเปลี่ยนสถานะ การเปิดหน้าแอดมินด้วย URL เพียงอย่างเดียวไม่ใช่การควบคุมสิทธิ์

---

## 🔍 ความท้าทายทางเทคนิคและการแก้ปัญหา (Engineering Challenges & Troubleshooting)

ในระหว่างการพัฒนาระบบ ได้ผ่านการ Debug เชิงลึกในหลายประเด็น:

* **Handling Large Payloads on Serverless:**
  * *ปัญหา:* การอัปโหลดไฟล์ภาพผ่าน Web Form ส่งตรงไปยัง Google Apps Script มักติดข้อจำกัดด้าน Request Format
  * *วิธีแก้:* แปลงไฟล์เป็น Base64 บนฝั่ง Client ด้วย JavaScript `FileReader` ก่อนส่งข้อมูลผ่าน `google.script.run` และใช้ `Utilities.base64Decode` ในการแปลงกลับเป็น Blob เพื่อเขียนลง Google Drive
* **Isolation Testing on Apps Script:**
  * *ปัญหา:* การ Debug ปัญหาระบบเชื่อมต่อระหว่าง LIFF กับ Apps Script ไม่แสดงผล Error ใน Log
  * *วิธีแก้:* ออกแบบฟังก์ชัน Mock Data Test (`testProcessFormData`) เพื่อทดสอบตรรกะฝั่ง Backend แยกอิสระจาก LIFF ช่วยระบุได้ชัดเจนว่าโค้ดหลังบ้านและการกำหนดสิทธิ์ (Permissions) ทำงานตามกรณีทดสอบที่กำหนด
* **LIFF Authentication & Security Scope:**
  * *ปัญหา:* อาการค้างที่ `liff.init()` และ `access.line.me refused to connect` เมื่อเปิดบน Desktop Browser
  * *วิธีแก้:* วิเคราะห์ลำดับ Lifecycle ของ LIFF SDK และตรวจสอบการตั้งค่า Callback URL ร่วมกับ Endpoint URL ให้สอดคล้องกับข้อกำหนดความปลอดภัยของ การเชื่อมต่อและการยืนยันตัวตนของ LINE

---

## 📄 License
เอกสารนี้เผยแพร่เพื่ออธิบายผลงานเท่านั้น ไม่ได้ให้สิทธิ์ใช้งานซอร์สโค้ดหรือข้อมูลภายในของระบบต้นทาง

> **การตั้งค่าความลับ:** เก็บ API key หรือ access token ใน Script Properties ของ Apps Script ไม่ใส่ค่าจริงลงในเอกสารหรือ Git และจำกัดสิทธิ์ผู้แก้ไขโปรเจกต์
