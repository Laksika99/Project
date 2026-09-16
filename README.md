#  Course Registration System (CLI) - Starter Template
**จัดทำโดย:** Thitiwat Thaicharoen (AIGUS__)

ระบบจัดการข้อมูลรายวิชาและการลงทะเบียนเรียนแบบ Command Line Interface (CLI) พัฒนาด้วยภาษา Python เพื่อเป็นโครงสร้างตัวอย่าง (Starter Template) สำหรับการศึกษาการจัดการข้อมูลไบนารีไฟล์ชนิด Fixed-length Record ด้วยโมดูล `struct`

---

##  Data Dictionary (โครงสร้างข้อมูลไบนารีทั้ง 3 ไฟล์)

การจัดเก็บข้อมูลในระบบจะแบ่งออกเป็น 3 ไฟล์หลัก โดยใช้โครงสร้างแบบ **Fixed-length Record** จัดเก็บด้วยรูปแบบ Little-Endian (`<`)

### 1. ไฟล์ข้อมูลนักศึกษา (`students.dat`) - ขนาด 97 Bytes / Record
| ฟิลด์ข้อมูล (Field) | ชนิดข้อมูล | ขนาด (Size) | คำอธิบาย (Description) |
| :--- | :--- | :--- | :--- |
| **student_id**   | `int` (`Q`)   | 8 Bytes  | รหัสนักศึกษา 13 หลัก (Unsigned Long Long)  |
| **student_code** | `str` (`15s`) | 15 Bytes | รหัสย่อ (UTF-8, Padding `\x00`)           |
| **name**         | `str` (`50s`) | 50 Bytes | ชื่อ-นามสกุล                              |
| **major**        | `str` (`20s`) | 20 Bytes | สาขาวิชา                                 |
| **year**         | `int` (`I`)   | 4 Bytes  | ชั้นปี (1-4)                               |
| **status**       | `int` (`I`)   | 4 Bytes  | สถานะ (`1` = Active, `0` = Deleted)     |

> **Format String:** `"<Q 15s 50s 20s I I"` | **RECORD_SIZE:** `97 Bytes`

---

### 2. ไฟล์ข้อมูลรายวิชา (`courses.dat`) - ขนาด 105 Bytes / Record
| ฟิลด์ข้อมูล (Field) | ชนิดข้อมูล | ขนาด (Size) | คำอธิบาย (Description) |
| :--- | :--- | :--- | :--- |
| **course_id**   | `int` (`I`)   | 4 Bytes  | รหัสไอดีรายวิชา                                     |
| **course_code** | `str` (`15s`) | 15 Bytes | รหัสวิชา (เช่น CS101)                               |
| **title**       | `str` (`50s`) | 50 Bytes | ชื่อรายวิชา                                         |
| **category**    | `str` (`20s`) | 20 Bytes | หมวดหมู่รายวิชา                                     |
| **credits**     | `int` (`I`)   | 4 Bytes  | จำนวนหน่วยกิต                                      |
| **fee**         | `float` (`f`) | 4 Bytes  | ค่าธรรมเนียมรายวิชา                                  |
| **status**      | `int` (`I`)   | 4 Bytes  | สถานะ (`1` = Active, `0` = Deleted)               |
| **is_full**     | `int` (`I`)   | 4 Bytes  | สถานะที่นั่ง (`0` = Available, `1` = Full)            |

> **Format String:** `"<I 15s 50s 20s I f I I"` | **RECORD_SIZE:** `105 Bytes`

---

### 3. ไฟล์ข้อมูลการลงทะเบียน (`enrollments.dat`) - ขนาด 36 Bytes / Record
| ฟิลด์ข้อมูล (Field) | ชนิดข้อมูล | ขนาด (Size) | คำอธิบาย (Description) |
| :--- | :--- | :--- | :--- |
| **enroll_id**   | `int` (`I`) | 4 Bytes    | รหัสการลงทะเบียน |
| **student_id**  | `int` (`Q`) | 8 Bytes    | อ้างอิงรหัสนักศึกษา (13 หลัก) |
| **course_id**   | `int` (`I`) | 4 Bytes    | อ้างอิงรหัสรายวิชา |
| **enroll_date** | `str` (`20s`) | 20 Bytes | วันเวลาลงทะเบียน (`YYYY-MM-DD HH:MM:SS`) |
| **status**      | `int` (`I`) | 4 Bytes | สถานะ (`1` = Active, `0` = Cancelled) |

> **Format String:** `"<I Q I 20s I"` | **RECORD_SIZE:** `36 Bytes`

---

## 🛠️ รายละเอียดเมนูการทำงาน (Application Design)

* **1) แสดงข้อมูลในระบบ (View Sub-menu):**
  * `1.1` ดูรายวิชาทั้งหมด (Courses)
  * `1.2` ดูรายชื่อนักศึกษาทั้งหมด (Students)
  * `1.3` ดูรายการลงทะเบียนทั้งหมด (Enrollments)
* **2) เพิ่มข้อมูลใหม่ (Add Record):** เพิ่มข้อมูลลงไฟล์ `.dat` ด้วย `struct.pack()`
* **3) แก้ไขข้อมูล (Update Record):** ค้นหาและเขียนทับตำแหน่งเดิมโดยใช้ `file.seek()` (In-Place Update)
* **4) ลบข้อมูล (Delete Record):** ปรับสถานะ `status` เป็น `0` (Soft Delete)
* **5) สร้างรายงานสรุป (Generate Report):** อ่านข้อมูลจากไฟล์ไบนารีมาประมวลผลแล้วบันทึกลง `report.txt`

---

## 📄 ตัวอย่างไฟล์รายงานสรุปที่ต้องสร้าง (`report.txt`)

```text
========================================================================================
                      Course Registration System - Summary Report
========================================================================================
Generated At : 2026-09-13 13:46:09
App Version  : 2.0
Endianness   : Little-Endian
Encoding     : UTF-8 (Fixed-length Record)
Data Files   : students.dat, courses.dat, enrollments.dat
========================================================================================

=== 1. รายวิชาทั้งหมด (Courses) ===
-------------------------------------------------------------------------------------------------------
| ID     | Code     | Title                        | Category     | Credits | Fee (THB)  | Status  | Full  |
-------------------------------------------------------------------------------------------------------
| 1001   | CS001    | Cyber Programming            | General      | 4       | 0.00       | Active  | No    |
| 1002   | CS002    | AI Design                    | Special      | 4       | 4500.00    | Active  | No    |
| 1003   | CS003    | Mobile Design                | Lab          | 4       | 3000.00    | Active  | No    |
| 1004   | CS004    | AI Programming               | Special      | 2       | 4500.00    | Active  | No    |
| 1005   | CS005    | Computer Design              | Special      | 4       | 4500.00    | Active  | No    |
| 1006   | CS006    | Computer Analytics           | Core         | 4       | 0.00       | Active  | No    |
| 1007   | CS007    | Database Security            | Special      | 4       | 1500.00    | Active  | No    |
| 1008   | CS008    | Network Design               | Special      | 1       | 1500.00    | Active  | Yes   |
| 1009   | CS009    | Software Design              | Elective     | 2       | 2500.00    | Active  | No    |
| 1010   | CS010    | Cyber Development            | Elective     | 2       | 4500.00    | Active  | No    |
| 1012   | CS012    | Cyber Design                 | Special      | 1       | 2500.00    | Deleted | No    |
-------------------------------------------------------------------------------------------------------

=== 2. รายชื่อนักศึกษา (Students) ===
---------------------------------------------------------------------------------------------------
| Student ID      | Code     | Name                       | Major              | Year | Status  |
---------------------------------------------------------------------------------------------------
| 6906022610001   | STD001   | กัญญารัตน์ สุขเจริญ           | Data Science       | 2    | Active  |
| 6906022610002   | STD002   | ณภัทร ไทยเจริญ               | Computer Science   | 4    | Active  |
| 6906022610003   | STD003   | ธีรภัทร ตั้งศิริ              | Cybersecurity      | 1    | Active  |
| 6906022610004   | STD004   | นภัสสร เดชะกุล              | Software Eng.      | 3    | Active  |
| 6906022610005   | STD005   | ปวริศ บุญมี                  | Information Tech   | 2    | Active  |
| 6906022610010   | STD010   | อนันต์ รัตนมณี                | Computer Science   | 4    | Deleted |
---------------------------------------------------------------------------------------------------

=== 3. ประวัติการลงทะเบียนเรียน (Enrollments) ===
---------------------------------------------------------------------------------------------------
| Enroll ID | Student ID      | Course ID | Date & Time         | Status    |
---------------------------------------------------------------------------------------------------
| 9001      | 6906022610001   | 1001      | 2026-09-01 09:15:22 | Active    |
| 9002      | 6906022610002   | 1005      | 2026-09-02 10:30:00 | Active    |
| 9003      | 6906022610003   | 1002      | 2026-09-05 14:12:45 | Active    |
| 9004      | 6906022610001   | 1008      | 2026-09-08 11:05:10 | Cancelled |
---------------------------------------------------------------------------------------------------

=== 4. สรุปสถิติรวมในระบบ (System Statistics) ===
* จำนวนรายวิชาทั้งหมด     : 11 วิชา (Active: 10, Deleted: 1)
* จำนวนนักศึกษาทั้งหมด   : 6 คน (Active: 5, Deleted: 1)
* รายการลงทะเบียนรวม     : 4 รายการ (Active: 3, Cancelled: 1)
* ค่าธรรมเนียมการศึกษาเฉลี่ย : 2,636.36 THB / วิชา

========================================================================================
                      End of Report - Course Registration System
========================================================================================
