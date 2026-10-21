# 📊 Automated Research Budget Tracker & Alert System

> **ขอบเขตการเผยแพร่:** เอกสารนี้เป็นกรณีศึกษาสำหรับ Portfolio โดยไม่รวมซอร์สโค้ดระบบจริง ภาพหน้าจอ เอกสารภายใน หรือข้อมูลผู้ใช้งาน ตัวอย่างข้อมูลใช้เพื่ออธิบายโครงสร้างเท่านั้น รายละเอียดการทำงานอ้างอิงคำอธิบายโครงการและยังไม่ได้ตรวจยืนยันกับระบบจริงในชุดไฟล์นี้


[![Google Apps Script](https://img.shields.io/badge/Google%20Apps%20Script-4285F4?style=flat&logo=google&logoColor=white)](https://developers.google.com/apps-script)
[![Google Sheets](https://img.shields.io/badge/Google%20Sheets-34A853?style=flat&logo=google-sheets&logoColor=white)](https://www.google.com/sheets/about/)
![Automation](https://img.shields.io/badge/Workflow-Automation-orange?style=flat)


> **ระบบติดตามงบประมาณโครงการวิจัยและแจ้งเตือนวันครบกำหนดอัตโนมัติแบบ Multi-Sheet บน Google Workspace**  
> ออกแบบและพัฒนาโดย: **กฤษวัฒน์ คัมภีพันธ์**

---

## 📌 Executive Summary (ภาพรวมโครงการ)

ในกระบวนการบริหารจัดการโครงการวิจัยที่มีหลายชุดโครงการ (Multi-Sheet/Sub-projects) มักพบปัญหาคอขวด 2 ประการหลัก:
1. **Human Error จากการติดตามวันครบกำหนด:** ขาดระบบแจ้งเตือนแบบ Proactive ทำให้ส่งมอบงานหรือรายงานล่าช้า
2. **การเบิกจ่ายเกินกรอบงบประมาณ:** ผู้บริหารและผู้รับผิดชอบไม่ได้รับข้อมูลสรุปภาพรวมงบประมาณคงเหลือจากข้อมูลที่บันทึกไว้ เมื่อเข้าใกล้จุดวิกฤต

**โซลูชันนี้ถูกพัฒนาขึ้นด้วย Google Apps Script ซึ่งใช้ภาษา JavaScript** เพื่อทำหน้าที่เป็น Background Service ตรวจสอบสถานะการเบิกจ่าย วันครบกำหนดส่งมอบ และส่งอีเมลแจ้งเตือนอัตโนมัติไปยังผู้รับผิดชอบเฉพาะโครงการและผู้บริหารภาพรวม เพื่อช่วยลดภาระการติดตามงานซ้ำด้วยมือ (Manual Follow-up)

---

## 🚀 Key Features (ฟังก์ชันการทำงานหลัก)

### 1. Dual-Level Automated Notifications
* **Task/Project Due Date Alert:** แจ้งเตือนล่วงหน้า $N$ วัน (เช่น 3 วัน) ก่อนครบกำหนดส่งมอบงาน โดยส่งตรงไปยังอีเมลของผู้รับผิดชอบโครงการนั้น ๆ จากแถวข้อมูล
* **Overall Sheet Budget Threshold Alert:** ตรวจจับว่างบประมาณภาพรวมของแต่ละชีตคงเหลือต่ำกว่าเกณฑ์ความเสี่ยงที่กำหนดหรือไม่ (เช่น $< 20\%$) หากต่ำกว่าจะส่งแจ้งเตือนสรุปงบประมาณไปยังอีเมลผู้บริหารระดับชีต

### 2. Multi-Sheet Batch Processing Architecture
* ออกแบบสถาปัตยกรรมให้รองรับการวนลูปประมวลผลหลายชีตตามลำดับ (`R01`, `R02`, ...) ภายใต้ Spreadsheet ไฟล์เดียวกันได้อย่างเป็นเอกเทศ

### 3. Smart State Management (PropertiesService)
* ป้องกันปัญหาการส่งอีเมลซ้ำซ้อน (Email Spamming):
  * **ระดับรายการ:** ใช้ In-Sheet Flagging เขียนสถานะ `"เตือนแล้ว"` กลับลงเซลล์ทันทีที่ส่งสำเร็จ
  * **ระดับงบประมาณภาพรวม:** นำ `PropertiesService.getUserProperties()` มาจัดเก็บ State ของยอดงบประมาณที่เคยเตือนไปแล้ว ทำให้ระบบจะส่งเตือนซ้ำก็ต่อเมื่องบประมาณลดลงกว่าระดับเดิมเท่านั้น

### 4. Zero-Code Configuration for Users
* ผู้ใช้งานทั่วไปสามารถเปลี่ยนชื่อหัวหน้าโครงการ และอีเมลรับแจ้งเตือนระดับชีตได้ผ่านช่องเซลล์ใน Google Sheet โดยตรง (`M1`, `N1`) โดยไม่ต้องแตะต้องโค้ดโปรแกรม

---

## 🏗 System Architecture & Workflow

```text
       [ Apps Script Time-Driven Trigger (Daily) ]
                           │
                           ▼
               mainBudgetTracker()
                           │
         ┌─────────────────┴─────────────────┐
         ▼                                   ▼
 [ Sheet R01 Process ]               [ Sheet R02 Process ]
         │                                   │
         ├──► 1. Check Due Dates             ├──► 1. Check Due Dates
         │       └─ Diff Days <= Threshold   │       └─ Diff Days <= Threshold
         │       └─ Send to Row Email (Col L)│       └─ Send to Row Email (Col L)
         │       └─ Mark Status: "เตือนแล้ว" │       └─ Mark Status: "เตือนแล้ว"
         │                                   │
         └──► 2. Check Overall Budget        └──► 2. Check Overall Budget
                 └─ Remaining <= 20%                 └─ Remaining <= 20%
                 └─ Check State (Properties)         └─ Check State (Properties)
                 └─ Send to Admin (Cell M1)          └─ Send to Admin (Cell M1)
```

---

## 📋 Data Structure (โครงสร้างชีตข้อมูล)

ระบบถูกออกแบบให้รองรับโครงสร้างตารางข้อมูลมาตรฐานดังนี้:

### ระดับชีต (Header Controls)
| เซลล์ | ฟังก์ชันการทำงาน | ตัวอย่างข้อมูล |
|:---:|:---|:---|
| **`M1`** | อีเมลรับแจ้งเตือนงบประมาณภาพรวมของชีต | `admin@example.com, lead@example.com` |
| **`N1`** | ชื่อผู้รับผิดชอบ/หัวหน้าโครงการ (สำหรับข้อความ "เรียน...") | `ผู้รับผิดชอบโครงการตัวอย่าง` |

### ระดับแถวข้อมูล (Row-level Data: เริ่มตั้งแต่แถวที่ 2)
| คอลัมน์ | ชื่อฟิลด์ | รายละเอียดการประมวลผล |
|:---:|:---|:---|
| **C** | ชื่อโครงการ / รายการ | ดึงมาเป็น Subject และ Body ของอีเมล |
| **D** | งบประมาณ (บาท) | คำนวณยอดรวมงบประมาณที่จัดสรร |
| **F** | ยอดเบิกจ่ายสะสม (บาท) | คำนวณยอดรวมเบิกจ่ายจริง |
| **G** | คงเหลือตามสัญญา (บาท) | แสดงยอดคงเหลือของแต่ละรายการ |
| **I** | วันที่ครบกำหนด | ตรวจสอบเปรียบเทียบกับวันที่ปัจจุบัน |
| **J** | สถานะแจ้งเตือน | สคริปต์จะเขียนทับค่าเป็น `"เตือนแล้ว"` เมื่อส่งสำเร็จ |
| **L** | อีเมลผู้รับผิดชอบโครงการ | ปลายทางอีเมลแจ้งเตือนวันครบกำหนดรายโครงการ |

---

## ✉️ Sample Notification Email

**หัวข้อ:** `[R01] แจ้งเตือนครบกำหนด: R001`  
**ผู้รับ:** `recipient@example.com`

```text
เรียน ผู้รับผิดชอบโครงการตัวอย่าง

โครงการวิจัย "R001" จะครบกำหนดในวันที่ 08/06/2026.
งบประมาณสำหรับรายการนี้: ฿100,000.00
ยอดคงเหลือปัจจุบันสำหรับรายการนี้: ฿15,000.00

กรุณาตรวจสอบและดำเนินการที่เกี่ยวข้อง.

ขอแสดงความนับถือ,
ทีมงานโครงการตัวอย่าง
ระบบแจ้งเตือนงบประมาณ
```

---

## 💻 Tech Stack & API Reference

* **Language:** JavaScript (ES6+), Google Apps Script (V8 Engine)
* **Services & APIs Used:**
  * `SpreadsheetApp`: อ่านและอัปเดตข้อมูลแบบ Batch 2D Arrays (`getValues()`, `setValues()`) เพื่อลดจำนวนครั้งในการอ่านและเขียนข้อมูล
  * `MailApp`: บริการจัดส่ง Transactional Email ภายในโควตาของ Google Workspace
  * `PropertiesService`: Persistent Key-Value Storage สำหรับ State Management ป้องกันแจ้งเตือนซ้ำ
  * `Utilities`: ฟอร์แมตวันที่และเวลาตาม Timezone ท้องถิ่น (`Session.getScriptTimeZone()`)

---

## 🛠 Setup & Installation Guide

> ขั้นตอนนี้อธิบายการตั้งค่าระบบต้นทาง ซอร์สโค้ดที่อ้างถึงไม่ได้รวมอยู่ในชุด Portfolio นี้ จึงยังไม่ใช่คู่มือสำหรับติดตั้งจากไฟล์ที่เผยแพร่โดยตรง

1. เปิด Google Sheet ที่ต้องการใช้งาน
2. ไปที่ **Extensions (ส่วนขยาย) > Apps Script**
3. คัดลอกโค้ดจากไฟล์ `budget_tracker.js` ไปวางในโปรเจกต์
4. ปรับแก้ตัวแปรคอนฟิกูเรชันหลัก (ถ้าจำเป็น):
   ```javascript
   const TRANSACTION_SHEET_NAMES = ["R01", "R02"]; // ระบุชื่อชีตที่ต้องการมอนิเตอร์
   const DAYS_BEFORE_DUE_TO_NOTIFY = 3;             // วันแจ้งเตือนล่วงหน้า
   const OVERALL_BUDGET_THRESHOLD_PERCENTAGE = 20;  // แจ้งเตือนเมื่องบเหลือน้อยกว่า 20%
   ```
5. บันทึกและทดสอบรันฟังก์ชัน `mainBudgetTracker` พร้อมกดยืนยันสิทธิ์ความปลอดภัย (Authorization)
6. ตั้งค่า **Triggers (ไอคอนนาฬิกา)**:
   * **Function:** `mainBudgetTracker`
   * **Event Source:** Time-driven
   * **Type:** Day timer (เลือกช่วงเวลา เช่น 07:00 - 08:00 น.)

---

## 📈 ประโยชน์ของแนวทาง (Expected Benefits)

* ⏱ **ลดเวลาการตรวจสอบเอกสาร (Time Savings):** ช่วยลดขั้นตอนการตรวจวันครบกำหนดด้วยมือของเจ้าหน้าที่พัสดุและการเงิน
* 🎯 **การติดตามวันครบกำหนด:** ช่วยให้ผู้รับผิดชอบทราบกำหนดส่งรายงานผ่านการแจ้งเตือนตามรอบเวลา
* 💰 **Cost-Effective Architecture:** ใช้โครงสร้างพื้นฐานเดิมบน Google Workspace โดยลดภาระการดูแลเซิร์ฟเวอร์แยก ทั้งนี้ขึ้นอยู่กับโควตาและแผนบริการที่ใช้

---

## 👤 Author

* **กฤษวัฒน์ คัมภีพันธ์ (Kritsawat Khampeepan)**
* Project Role: Developer & Workflow Architect
