# แบบฝึกหัดที่ 5: ทำ Hybrid Conversation ด้วย Agent Orchestration + Knowledge

🔑 **ต้องการ M365 Copilot License + สิทธิ์เข้าใช้ Copilot Studio**

แบบฝึกหัดนี้จะพาเราต่อยอด **Financial Report Assistant** ที่สร้างไว้ใน Module 3 ให้รองรับการคุยแบบผสมได้มากขึ้น โดยผู้ใช้ยังคงทำงานรายงานการเงินแบบ structured ได้ และในบทสนทนาเดียวกันก็ถามความหมายของ technical term จาก knowledge ได้ด้วย

จุดสำคัญของแบบฝึกหัดนี้คือ **ไม่ต้องสร้าง Topic ใหม่** ให้เริ่มจาก Agent เดิมที่มี `Monthly Report Intake` อยู่แล้ว แล้วปรับ **Instructions** และ **Orchestration** ให้ Agent ตัดสินใจเส้นทางบทสนทนาได้ลื่นขึ้น

```mermaid
flowchart TD
    A[User message] --> B{ประเภทคำถาม}
    B -->|ขอสร้าง/แก้รายงาน| C[Monthly Report Intake topic]
    B -->|ถาม technical term| D[Agent orchestration + Knowledge]
    B -->|นอกขอบเขต| E[Fallback]
    C --> F[ตอบกลับแบบ structured]
    D --> G[ตอบกลับแบบ grounded ด้วย knowledge]
    E --> H[ขอให้ผู้ใช้ถามใหม่อย่างชัดเจน]
```

---

## ก่อนเริ่ม

1. ต้องทำ Exercise 1-4 ของ Module 3 มาก่อน
2. ต้องมี Agent เดิมที่มี Topic `Monthly Report Intake` พร้อมใช้งาน
3. ในแบบฝึกหัดนี้ ให้ใช้ไฟล์ความรู้เรื่อง technical terms เดิม:
   - `financial-report-technical-terms-knowledge.docx`

> ⚠️ **Note:** แบบฝึกหัดนี้เน้นให้ Agent เดิมคุยได้ทั้งงานรายงานและคำถามความรู้ในบทสนทนาเดียวกัน โดยยังไม่ต้องสร้าง Topic ใหม่

---

## Practice 1: เตรียม Knowledge ให้พร้อม (Technical Terms)

1. ไปที่แท็บ **Knowledge** ของ Agent
2. กด **Add knowledge**
   ![alt text](./images/click-add-knowledge.png)
3. อัปโหลดไฟล์

   ```text
   financial-report-technical-terms-knowledge.docx
   ```

   ![alt text](./images/upload-knowledge-files.png)
4. กด **Add to agent**
   ![alt text](./images/add-files-to-agent.png)
5. ตรวจสถานะให้เป็น **Ready** ก่อนเริ่มทดสอบ
   ![alt text](./images/check-knowledge-status.png)

> 💡 Tip: ถ้า status ของไฟล์ยังเป็น `In Progress` ตัว knowledge จะยังไม่สามารถนำมาใช้ได้

---

## Practice 2: ปรับ Agent Instructions ให้รองรับ hybrid conversation

1. ไปที่หน้า **Overview** ของ Agent แล้วแก้ส่วน **Instructions**
2. เพิ่มข้อความให้ชัดว่า Agent รองรับทั้งการทำรายงานรายเดือน และการอธิบาย technical term
3. ใช้ตัวอย่างนี้แล้วปรับให้เหมาะกับบริบททีมของคุณ

```text
You are Financial Report Assistant for enterprise business users.

Scope:
- Help users create and revise monthly financial report analysis.
- Explain financial reporting technical terms using approved knowledge.

Rules:
- If user asks to create or revise a monthly report, use the structured flow in Monthly Report Intake.
- If user asks the meaning of financial reporting technical terms, answer with grounded knowledge and keep the explanation concise.
- If the request is outside finance reporting scope, ask the user to rephrase within scope.
```

4. กด **Save**

> ⚠️ **Note:** ในแบบฝึกหัดนี้ยังไม่ต้องเพิ่ม trigger ใหม่หรือสร้าง Topic ใหม่

---

## Practice 3: เปิด orchestration เพื่อให้คุยแบบผสมได้

1. ไปที่ **Settings** ของ Agent
2. ตรวจส่วน **Orchestration** ให้เป็นโหมด generative เพื่อให้ Agent ตัดสินใจเส้นทางบทสนทนาได้
3. บันทึกการตั้งค่า

> 💡 Tip: ใน Exercise 2 เราจะต่อยอดจาก Topic เดิมอีกครั้ง แต่จะเพิ่ม action สำหรับส่งรายงานต่อให้ครบกระบวนการ

---

## Practice 4: ทดสอบ hybrid conversation (structured + generative)

ให้ทดสอบใน **Test your agent** ตามลำดับนี้

1. เริ่มด้วย structured request

   ```text
   สร้าง draft รายงานการเงินเดือน May ของ BU Aromatics
   ```

2. สลับเป็นคำถามเชิง technical term ในบทสนทนาเดียวกัน

   ```text
   Variance Percent คืออะไร และควรตีความอย่างไรในรายงานรายเดือน
   ```

3. ทดสอบอีกคำถามเชิงความรู้

   ```text
   EBITDA margin ต่างจาก gross margin อย่างไร
   ```

4. ทดสอบ out-of-scope 1 เคส

   ```text
   ช่วยแนะนำร้านกาแฟใกล้ออฟฟิศ
   ```

สิ่งที่ต้องสังเกต:
- Agent ยังทำ structured flow สำหรับงานรายงานได้
- Agent ตอบคำถาม technical term ได้โดยอิง knowledge
- คำถามนอกขอบเขตไม่ควรถูกตอบมั่ว

---

## สรุป

ในแบบฝึกหัดนี้ คุณได้เปิดประสบการณ์ hybrid conversation ให้กับ Financial Report Assistant โดยใช้ Agent orchestration และ knowledge เดิม ทำให้ผู้ใช้คุยได้ทั้งงานโครงสร้างและคำถามความรู้ในบริบทเดียวกัน โดยยังไม่ต้องสร้าง Topic ใหม่

ขั้นตอนถัดไป → [เพิ่ม Tool และ เพิ่ม Agent Flow สำหรับส่งรายงาน](../exercise-2-approval-flow-action/README.md)

   > ⚠️ Note: ในแบบฝึกหัดนี้ให้ใช้ `UserKnowledgeQuestion` เหมือนกับ branch แรก เพื่อให้ทั้ง 2 เส้นทางรับคำถามจากตัวแปรเดียวกัน และเปรียบเทียบผลการ route ได้ง่าย

