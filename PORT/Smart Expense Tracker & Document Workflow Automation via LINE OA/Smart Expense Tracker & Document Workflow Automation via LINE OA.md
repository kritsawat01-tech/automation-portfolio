# 📌 Smart Expense Tracker & Document Workflow Automation via LINE OA

> **ขอบเขตการเผยแพร่:** เอกสารนี้เป็นกรณีศึกษาสำหรับ Portfolio โดยไม่รวมซอร์สโค้ดระบบจริง ภาพหน้าจอ เอกสารภายใน หรือข้อมูลผู้ใช้งาน ตัวอย่างข้อมูลใช้เพื่ออธิบายโครงสร้างเท่านั้น รายละเอียดการทำงานอ้างอิงคำอธิบายโครงการและยังไม่ได้ตรวจยืนยันกับระบบจริงในชุดไฟล์นี้


[![Google Apps Script](https://img.shields.io/badge/Google%20Apps%20Script-4285F4?style=for-the-badge&logo=google&logoColor=white)](https://developers.google.com/apps-script)
[![LINE Messaging API](https://img.shields.io/badge/LINE%20Messaging%20API-00C300?style=for-the-badge&logo=line&logoColor=white)](https://developers.line.biz/)
[![LIFF](https://img.shields.io/badge/LINE%20Front--end%20Framework-00B900?style=for-the-badge&logo=line&logoColor=white)](https://developers.line.biz/en/docs/liff/)
[![TailwindCSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)](https://tailwindcss.com/)

> **ระบบอัตโนมัติแบบครบวงจร (End-to-End Enterprise Automation)** บน LINE Official Account สำหรับบันทึกค่าใช้จ่ายโครงการ จัดหมวดหมู่อัตโนมัติ (Auto-categorization) และระบบติดตามเอกสารราชการ/ภายในองค์กร (Internal Document Workflow & Tracking System) ขับเคลื่อนด้วย Google Workspace services (Google Apps Script, Google Sheets, Google Drive)

---

## 📖 สรุปภาพรวมโครงการ (Executive Summary)

โปรเจกต์นี้ถูกออกแบบและพัฒนาขึ้นเพื่อแก้ปัญหาคอขวด (Bottleneck) ในการดำเนินงานขององค์กร 2 ด้านหลัก:
1. **ระบบบันทึกค่าใช้จ่ายโครงการพร้อมแนบสลิป (Automated Expense Tracking):** ลดความซ้ำซ้อนในการกรอกเอกสาร ลด Human Error และจัดหมวดหมู่ค่าใช้จ่ายโดยอัตโนมัติผ่านแชท LINE
2. **ระบบรับ-ส่งและติดตามสถานะเอกสารราชการ/ภายใน (Document Routing & Status Tracking):** ใช้งานผ่าน LINE Front-end Framework (LIFF) พร้อมระบบจัดการหลังบ้าน (Admin Dashboard) สำหรับธุรการ และ Push Notification แจ้งเตือนสถานะเมื่อมีการบันทึกหรือเปลี่ยนสถานะ

---

## 🎯 ปัญหาและแนวทางแก้ไข (Problem & Solution)

| ปัญหาเดิม (Pain Points) | แนวทางแก้ไขด้วยระบบนี้ (Our Solution) |
| :--- | :--- |
| พนักงานต้องพิมพ์รายงานค่าใช้จ่ายลงกระดาษ/Excel ซ้ำซ้อน และสลิปหลักฐานมักสูญหาย | พิมพ์ส่งผ่าน LINE แชทปกติ `รหัส รายการ ราคา` ตามด้วยรูปสลิป ระบบจัดเก็บลง Google Drive และแนบลิงก์ลง Google Sheet ตามโครงการให้อัตโนมัติ |
| ค่าใช้จ่ายไม่ถูกจัดหมวดหมู่ ทำให้สรุปงบประมาณสิ้นปีล่าช้า | **Rule-based Keyword Engine** ตรวจจับคำในชื่อรายการแล้วระบุหมวดหมู่อัตโนมัติ (ค่าตอบแทน, ค่าใช้สอย, ค่าวัสดุ, ครุภัณฑ์) |
| เอกสารส่งข้ามหน่วยงานตกหล่น ผู้ส่งไม่ทราบว่าเรื่องดำเนินการถึงขั้นตอนใด | ส่งคำร้องผ่าน **LIFF Web App** ใน LINE พร้อมระบบ **Admin Dashboard** ให้ธุรการอัปเดตสถานะและแนบรูปหลักฐาน พร้อมแจ้งเตือนกลับหาผู้ส่งแบบอัตโนมัติ (Push Notification) |

---

## 🏗 สถาปัตยกรรมระบบ (System Architecture)

```
                           +--------------------------------+
                           |       LINE Client (User)       |
                           +--------------------------------+
                                   |                 ^
             1. Chat (Expense/Slip)|                 | 5. Push Notification
             2. Rich Menu -> LIFF  |                 |    (Status Update)
                                   v                 |
                        +-------------------------------+
                        |    LINE Messaging API / LIFF  |
                        +-------------------------------+
                                        | Webhook / RPC
                                        v
                        +-------------------------------+
                        |   Google Apps Script Engine   |
                        |          (Code.gs)            |
                        +-------------------------------+
                         /              |              \
                        /               |               \
                       v                v                v
      +--------------------+  +--------------------+  +--------------------+
      |    Google Drive    |  |    Google Sheets   |  |  Admin Dashboard   |
      | (Slips & Receipts) |  | (Multi-DB Storage) |  |   (Web App GUI)    |
      +--------------------+  +--------------------+  +--------------------+
```

---

## 🌟 ฟีเจอร์เด่น (Key Features)

### 1. ระบบบันทึกค่าใช้จ่ายและแนบสลิปอัจฉริยะ (Smart Expense Logger)
* **สองขั้นตอนแบบต่อเนื่อง (2-Step Context Caching):** ส่งข้อความ `รหัส รายการ จำนวนเงิน` ตามด้วยรูปภาพสลิป ระบบใช้ `CacheService` เก็บบริบทชั่วคราว โดยกำหนดอายุแคช 5 นาที และต้องรองรับกรณีแคชหมดอายุก่อนจับคู่สำเร็จ เพื่อจับคู่รูปเข้ากับแถวข้อมูลในชีต
* **จัดหมวดหมู่อัตโนมัติ (Rule-based Keyword Categorization):** สแกนคำค้นในชื่อรายการและจับคู่เข้ากับ 4 หมวดหลัก:
  * ค่าตอบแทน
  * ค่าใช้สอย
  * ค่าวัสดุ
  * ครุภัณฑ์
* **แยกโฟลเดอร์โครงการอัตโนมัติ:** ค้นหาโฟลเดอร์ Google Drive ตามรหัสโครงการ หากยังไม่มี ระบบจะสร้างโฟลเดอร์ใหม่ให้อัตโนมัติ

### 2. ระบบส่งและติดตามเอกสาร (Document Routing & Tracking)
* **ฟอร์มผ่าน LINE Front-end Framework (LIFF):** พนักงานกรอกรายละเอียด (ด่วนที่สุด/ลับ/ส่งให้/รับจาก) และแนบไฟล์ (PDF, PNG, JPG) โดยแปลงเป็น Base64 ส่งเข้า Drive
* **ผูก LINE User Identity อัตโนมัติ:** ดึง `displayName` และ `userId` ของผู้ส่งผ่าน LIFF SDK เพื่อเชื่อมโยงรายการกับผู้ส่งและใช้ส่ง Push Notification โดยต้องตรวจสอบตัวตนและสิทธิ์ฝั่งเซิร์ฟเวอร์ด้วย
* **แดชบอร์ดธุรการ (Admin Dashboard):** หน้าเว็บสำหรับธุรการจัดการสถานะ โดยต้องบังคับใช้สิทธิ์ฝั่งเซิร์ฟเวอร์:
  * `ยังไม่ส่ง` ➡️ `อยู่ระหว่างดำเนินการ` ➡️ `ส่งแล้ว`
  * อัปโหลดรูปภาพยืนยันการเซ็นรับเอกสารเข้า Google Drive
* **แจ้งเตือนสถานะย้อนกลับ (Status Push Notification):** ระบบ Push Message หาทั้งธุรการเมื่อมีคำร้องใหม่ และหาผู้ส่งเอกสารเมื่อมีการเปลี่ยนสถานะ

---

## 🛠 Tech Stack & Tools

* **Frontend:** HTML5, CSS3, [TailwindCSS](https://tailwindcss.com/) (CDN), JavaScript (ES6+), LINE Front-end Framework (LIFF v2)
* **Backend:** [Google Apps Script (GAS)](https://developers.google.com/apps-script)
* **Database & Storage:** Google Sheets API, Google Drive API
* **APIs & Messaging:** LINE Messaging API (Webhook, Reply Message, Push Message)

---

## 📂 โครงสร้างระบบที่อธิบาย (System Structure)

```text
├── Code.gs                   # Backend logic (Webhooks, Drive/Sheets CRUD, Routing)
├── liff-form.html            # LIFF Web form for document submissions
├── admin-dashboard.html      # Responsive Admin Dashboard for document management
├── index.html                # Interactive SOP Simulator for staff onboarding
└── README.md                 # Project documentation & portfolio showcase
```

---

## ⚙️ โครงสร้างฐานข้อมูล (Database Schema)

### ชีตที่ 1: ระบบค่าใช้จ่าย (`Expense Sheet`)
| คอลัมน์ | ชื่อฟิลด์ | รายละเอียด |
| :---: | :--- | :--- |
| A | ลำดับ | Auto-increment sequence number |
| B | รหัสอ้างอิง | รหัสโครงการ (เช่น `7001`, `R01`) |
| C | รายการ | รายละเอียดค่าใช้จ่าย |
| D | หมวดหมู่ | ตรวจจับอัตโนมัติจาก Keyword (ค่าตอบแทน, ค่าใช้สอย ฯลฯ) |
| E | ราคา | จำนวนเงิน (Number format) |
| F | ผู้ส่ง | LINE Display Name |
| G | วัน-เวลาที่บันทึก | Timestamp |
| H | ลิงก์สลิป | URL รูปภาพใน Google Drive |

### ชีตที่ 2: ระบบจัดการเอกสาร (`Document Tracking Sheet`)
| คอลัมน์ | ชื่อฟิลด์ | รายละเอียด |
| :---: | :--- | :--- |
| A | เลขที่อ้างอิง | Document ID (e.g., `DOC-20250101-143022`) |
| B | วันที่ส่ง | Timestamp |
| C | หัวข้อหนังสือ | ชื่อเรื่องหรือหัวข้อเอกสาร |
| D | ความเร่งด่วน | ปกติ / ด่วน / ด่วนที่สุด |
| E | ชั้นความลับ | ปกติ / ลับ / ลับที่สุด |
| F | ประเภทรายการ | ส่งเอกสารให้ / รับเอกสารจาก |
| G | จาก/ถึง หน่วยงาน | ชื่อหน่วยงานปลายทาง/ต้นทาง |
| H | ผู้ส่ง (LINE) | LINE Display Name |
| I | User ID ผู้ส่ง | LINE User ID (สำหรับ Push Message) |
| J | ลิงก์ไฟล์แนบ | Google Drive File URL |
| K | สถานะ | ยังไม่ส่ง / อยู่ระหว่างดำเนินการ / ส่งแล้ว |
| L | หมายเหตุ | ข้อมูลเพิ่มเติม |
| M | ลิงก์รูปยืนยัน | URL รูปภาพหลักฐานการเซ็นรับ |

---

## 🚀 ประโยชน์ต่อกระบวนการทำงาน (Workflow Benefits)

* **ลดระยะเวลาการทำงาน (Efficiency):** ช่วยลดขั้นตอนการนำส่งเอกสารและบันทึกสลิปผ่านสมาร์ทโฟน
* **ความถูกต้องของข้อมูลงบประมาณ (Data Accuracy):** ระบบคัดกรองหมวดหมู่ค่าใช้จ่ายอัตโนมัติ ช่วยลดการระบุหมวดหมู่ด้วยมือ โดยผู้ใช้งานยังต้องตรวจสอบความถูกต้อง
* **ความโปร่งใสในองค์กร (Operational Transparency):** พนักงานเรียกดูสถานะที่บันทึกไว้ในระบบได้ ลดภาระงานธุรการในการตอบคำถามความคืบหน้าของเอกสาร
