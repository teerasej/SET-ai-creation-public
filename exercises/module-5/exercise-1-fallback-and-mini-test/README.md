# แบบฝึกหัดที่ 7: ออกแบบ Fallback และ Escalate อย่างปลอดภัย

🔑 **ต้องการ M365 Copilot License + สิทธิ์เข้าใช้ Copilot Studio**

แบบฝึกหัดนี้จะพาเรา harden Agent เดิมให้พร้อมใช้งานจริงมากขึ้น โดยเน้น 2 เรื่องสำคัญคือ **Fallback** และ **Escalation** เพื่อให้ Agent ไม่ตอบมั่วเมื่อคำถามไม่ชัดเจน และรู้ว่าควรจบการสนทนาอย่างปลอดภัยเมื่ออยู่นอกขอบเขต

แบบฝึกหัดนี้ต่อยอดจาก flow เวอร์ชันที่เรียบง่ายขึ้นใน Module 3-4 ซึ่ง Agent จะรับค่า **Report Format**, รับไฟล์ Excel, แสดงผลวิเคราะห์ในแชต และใน Module 4 Exercise 2 สามารถส่งผลสรุปทางอีเมลโดยใช้ตัวแปรหลัก `FinancialAnalysisResult` และ `ReviewerEmail`

```mermaid
flowchart TD
    A[User Utterance] --> B{ตรงกับ topic ไหม}
    B -->|ตรง| C[Run target topic]
    B -->|ไม่ตรง| D[Fallback Topic]
    D --> E[Message: ขอข้อมูลให้ชัดขึ้น]
    E --> F[ผู้ใช้ถามใหม่หรือเลือกขอความช่วยเหลือ]
    F --> B
    C --> G{ไปต่อได้ไหม}
    G -->|ได้| H[ตอบกลับตาม flow เดิม]
    G -->|ไม่ได้| I[Escalate Topic]
    I --> J[Safe completion]
```

---

## ก่อนเริ่ม

1. ให้แน่ใจก่อนว่าได้ทำ Module 3-4 แล้ว และยังมี Agent เดิมที่ใช้โจทย์วิเคราะห์รายงานการเงินอยู่
2. ใน Agent ควรมี Topic หลักที่รับคำขอ เช่นสรุปรายงานรายเดือน หรือถามความรู้ด้านรายงานการเงิน
3. แบบฝึกหัดนี้จะไม่ได้เพิ่มความสามารถใหม่ขนาดใหญ่ แต่จะช่วยให้ Agent ตอบอย่างปลอดภัยและคาดเดาได้มากขึ้น

> ⚠️ **Note:** ใน Agent `Fallback` และ `Escalate` เป็น **system topics** ที่มีมาให้ใน Agent อยู่แล้ว เราปรับแต่งได้ แต่ควรปรับอย่างระมัดระวังและทดสอบทุกครั้งหลังแก้ไข

---

## Practice 1: ปรับ Fallback system topic ให้ถามกลับอย่างมีบริบท

1. จาก Menu Agent ด้านบนไปที่ **Topics > System > Fallback**
   ![เปิด System topics](./images/open-system-topics.png)
2. เปิด Topic Fallback แล้วดูข้อความเดิมของระบบ
   
3. ปรับข้อความใน **Message node** ให้เหมาะกับงานวิเคราะห์รายงานการเงิน โดยบอกผู้ใช้ชัดเจนว่าควรระบุอะไรเพิ่ม เช่น รูปแบบรายงาน, ช่วงข้อมูลที่ต้องการสรุป, หรือสิ่งที่ต้องการให้ช่วย

   ตัวอย่างข้อความ:

   ```text
   ขอโทษครับ ผมยังจับคำขอนี้ไปยังหัวข้อที่ถูกต้องไม่ได้
   ลองพิมพ์ใหม่โดยระบุรูปแบบรายงานและสิ่งที่ต้องการ เช่น
   - สรุปรายงานการเงินรายเดือนแบบ Executive summary
   - วิเคราะห์ต้นทุนจากไฟล์ที่อัปโหลดและสรุปเป็น bullet
   - อธิบายความหมายของ EBITDA
   ```
   ![แก้ Message node ใน Fallback](./images/edit-fallback-message-node.png)
4. กด **Save**
5. กดปุ่ม **Settings** เพื่อเข้าไปปรับค่า orchestration ของ Agent
   ![เปิด Agent settings](./images/open-agent-settings.png)
6. ในส่วน **Orchestration** ให้เลือกโหมด **No - Use classic orchestration** เพื่อให้การ route ไป system topics คาดเดาได้ง่ายขึ้นระหว่างฝึกทดสอบ
   ![ตั้งค่า classic orchestration](./images/set-classic-orchestration.png)
7. กลับมาที่ Overview ของ Agent และแก้ 2 บรรทัดแรกของ Instructions ให้สอดคล้องกับข้อความใน Fallback มากขึ้น เช่น

   ```text
   You are Financial Report Assistant for enterprise business users.
   Only answer questions about financial report analysis, financial reporting terminology, and report distribution policy. Do not answer HR, leave, travel, food, facilities, or general office questions.
   ```

8. กดปุ่ม **Save** ในส่วน instruction
9. เปิด **Test your agent** และลองพิมพ์คำถามที่ไม่เกี่ยวข้อง เช่น

   ```text
   ขอข้อมูลร้านกาแฟใกล้ออฟฟิศ
   ```

10. **Expected result:** ระบบควรเข้า `Fallback` topic และตอบกลับด้วยข้อความที่ช่วยให้ผู้ใช้ถามใหม่ได้ชัดขึ้น

---

## Practice 2: ปรับ Escalate system topic ให้จบการสนทนาอย่างปลอดภัย

1. ไปที่ **Topics > System > Escalate**
2. ตรวจดูว่า Topic นี้สื่อสารกับผู้ใช้อย่างไรเมื่อ Agent ต้องพาไปสู่การคุยกับคนหรือหยุดตอบในเรื่องที่เกินขอบเขต
3. ปรับข้อความให้เหมาะกับบริบทธุรกิจจริง โดยหลีกเลี่ยงการสัญญาว่าระบบจะเปิด ticket หรือส่งต่ออัตโนมัติ ถ้ายังไม่ได้สร้าง flow นั้นใน Agent

   ตัวอย่างข้อความ:

   ```text
   คำขอนี้อาจต้องให้ผู้รับผิดชอบตรวจสอบเพิ่มเติมครับ
   หากเป็นประเด็นเชิงนโยบาย การอนุมัติการเผยแพร่รายงาน หรือข้อมูลที่ต้องการการยืนยันอย่างเป็นทางการ
   กรุณาติดต่อทีม Finance Analyst หรือ Shared Services ตามช่องทางขององค์กร
   ```

4. ทดสอบอย่างน้อย 2 เคส:
   - ผู้ใช้ขอคุยกับคนโดยตรง
   - ผู้ใช้ถามเรื่องนโยบายหรือการอนุมัติที่ Agent ไม่ควรตัดสินใจแทน

---

---

## สรุป

ในแบบฝึกหัดนี้ คุณได้ทำให้ Agent แข็งแรงขึ้นด้วยการปรับ **Fallback** และ **Escalate** system topics ให้เหมาะกับงานจริง เพื่อให้การตอบนอกขอบเขตมีความปลอดภัยและคาดเดาได้มากขึ้น

ขั้นตอนถัดไป → [ทำ Mini test cycle ด้วย Test your agent](../exercise-2-mini-test-cycle/README.md)

อ่านเพิ่มเติมได้ที่:
- [Microsoft Learn: Use system topics](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-system-topics)
- [Microsoft Learn: Test your agent](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-test-bot)
