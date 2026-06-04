# Module 2 Files

ไฟล์ในโฟลเดอร์นี้ใช้สำหรับแบบฝึกหัด Module 2 (Copilot Studio Core Build)

## Dataset

- `CPALL-Monthly-Financial-Report-May2026.xlsx`
- `CPALL-Monthly-Financial-Report-June2026.xlsx`

## Knowledge Documents (Exercise 5)

- `financial-report-technical-terms-knowledge.docx`
- `financial-report-distribution-policy-knowledge.docx`

## Usage

- Use the May workbook for the first pass of the Excel analysis exercise.
- Use the June workbook to compare month-over-month changes or to retry the same flow with different values.
- Use `financial-report-technical-terms-knowledge.docx` for the technical terms branch in Exercise 5.
- Use `financial-report-distribution-policy-knowledge.docx` for the policy branch in Exercise 5.

## Workbook Structure

1. `Summary`
   - สรุปภาพรวมราย Business Unit เช่น Revenue, Cost, Variance, Status และ Key Risk
2. `Revenue`
   - รายได้ตาม Product Category พร้อมค่า target และ variance
3. `Costs`
   - ค่าใช้จ่ายตามหมวดต้นทุน พร้อม budget และ YoY change
4. `Variance_Analysis`
   - ตารางเจาะลึกค่า Actual vs Budget และ root cause สำหรับใช้ทำ narrative ในรายงาน

## Notes

- ตัวเลขทั้งหมดเป็นข้อมูลจำลองเพื่อการฝึกอบรม
- ชื่อคอลัมน์ใช้ภาษาอังกฤษเพื่อให้ง่ายต่อการ map ตัวแปรใน Copilot Studio