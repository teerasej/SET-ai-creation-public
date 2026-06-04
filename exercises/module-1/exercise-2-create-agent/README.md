# แบบฝึกหัดที่ 2: สร้าง Agent ตัวแรกด้วย Copilot Studio

🔑 **ต้องการ M365 Copilot License + สิทธิ์เข้าใช้ Copilot Studio**

ในแบบฝึกหัดนี้ เราจะสร้าง **AI Agent** ตัวแรกของเราผ่าน **Copilot Studio** โดยใช้ **Prompt** เพื่อบอกหน้าที่ของ Agent ให้ชัดเจนตั้งแต่ต้น เป้าหมายคือสร้างผู้ช่วย HR สำหรับพนักงาน CPAll ที่ช่วยตอบคำถามเรื่อง **การเบิกค่าใช้จ่าย** และ **ข่าวสารหรือประกาศขององค์กร**

> ⚠️ **หมายเหตุ:** ถ้ายังไม่มีสิทธิ์เข้า Copilot Studio ในองค์กร ให้ขอจาก IT Admin หรือทดลองดูขั้นตอนไปพร้อมกับเพื่อนที่มี license ก่อนได้เลย

---

## ขั้นตอนที่ 1: เข้าสู่ Copilot Studio

1. เปิดเบราว์เซอร์ไปที่ [https://copilotstudio.microsoft.com](https://copilotstudio.microsoft.com)

2. Login ด้วย Microsoft account ขององค์กร

3. ตรวจสอบว่าอยู่ใน **Environment** ที่ถูกต้อง (สังเกตที่มุมขวาบน ควรแสดงชื่อองค์กร หรือชื่อ Environment ที่กำหนด)

   ![ภาพหน้า Login Copilot Studio](./images/copilot-studio-login.png)

---

## ขั้นตอนที่ 2: สร้าง Agent ใหม่

1. จากเมนูด้านซ้าย ให้คลิกที่ **Agent** เพื่อแสดงรายการ Agent ที่มีอยู่ใน Environment (ถ้ามี)
   ![alt text](./images/agent-list.png)


2. ในช่อง prompt ด้านบน จะเป็นส่วนที่ทำให้เราสามารถสร้าง Agent ด้วยการพิมพ์อธิบายหน้าที่ให้เป็นภาษาธรรมชาติ (Natural Language) 

   ![ภาพโหมด Describe](./images/describe-agent.png)

---

## ขั้นตอนที่ 3: กำหนดหน้าที่ให้ Agent ด้วย Natural Language

1. ในช่อง Prompt ให้คัดลอกข้อความด้านล่างนี้ไปวาง แล้วกด Enter:

   ```
   You are a helpful HR assistant for CPAll employees. You help employees understand expense claim policy, and provide organization news or announcements. 
   ```

2. Copilot Studio จะตอบรับและสรุปขอบเขตการทำงานของ Agent ให้ จากนั้นจะมีการกำหนดชื่อชั่วคราวให้ Agent นี้ เช่น "HR Assistant" หรือ "HR Bot" ขึ้นอยู่กับระบบ

   ![ภาพสรุปหน้าที่ Agent](./images/draft-agent.png)

3. กดปุ่ม Edit และตั้งชื่อ Agent ว่า:

   ```
   PTT HR Assistant [ชื่อตัวเอง]
   ```

4. กด ปุ่ม Save และรอให้ระบบยืนยัน

---
## ขั้นตอนที่ 4: ทดสอบ Agent

1. สังเกตด้านขวาของหน้าจอ จะมีหน้าต่างพรีวิว Agent พร้อมให้ทดสอบทันที

   ![ภาพทดสอบ Agent](./images/test-agent.png)

2. พิมพ์คำถามทดสอบต่อไปนี้ แล้วดูว่า Agent ตอบอย่างไร:

   ```
   ถ้าจะเดินทางไปประชุม เบิกค่าอะไรได้บ้าง?
   ```

3. ลองถามต่ออีก 1 ข้อในหัวข้อข่าวสารองค์กร:

   ```
   มีข่าวกิจกรรมล่าสุดอะไรบ้าง?
   ```

4. ตรวจสอบว่า Agent ตอบคำถามได้ตรงประเด็นหรือไม่ ซึ่งในขั้นตอนนี้คำตอบมักยังเป็นคำตอบแบบกว้างๆ หรือเชิงแนะนำทั่วไป เพราะเรายังไม่ได้เพิ่ม Knowledge จริงขององค์กรเข้าไป

5. ทดสอบอีกครั้งกับคำถามที่อยู่นอกขอบเขต เพื่อดูว่า Agent ปฏิเสธอย่างไร:

   ```
   ช่วยแนะนำร้านอาหารใกล้สำนักงานหน่อย
   ```

6. Agent ควรปฏิเสธคำถามข้อนี้ เนื่องจากอยู่นอกขอบเขตที่กำหนดไว้

---


## ขั้นตอนที่ 5: เพิ่มคำสั่งเสริม ใน Instruction (ถ้าต้องการ)

1. คุณสามารถเพิ่มรายละเอียดขอบเขตการทำงานให้ชัดขึ้นได้ โดยการกดปุ่ม **Edit** และเพิ่มคำสั่งในส่วน Instruction เข้าไปด้านล่างของเดิม เช่น:

   ```
   Always answer in Thai language. Keep responses concise and professional. Only answer questions related to HR and internal organization communication topics. If the user asks about expense claims or organization news and you do not have enough confirmed information, clearly say that user need to consult HR or internal resources for accurate information.
   ```
   ![ภาพเพิ่ม Instruction](./images/set-instruction.png)

2. กด **Save** เพื่อบันทึก instruction ใหม่
3. ทดสอบอีกครั้งกับคำถามชุดเดิม:
   ```
   ถ้าจะเดินทางไปประชุม เบิกค่าอะไรได้บ้าง?
   ```
   ```
   ช่วยแนะนำร้านอาหารใกล้สำนักงานหน่อย
   ```

> 💡 **เคล็ดลับ:** การเขียนคำสั่ง (Instruction) ให้ Agent ชัดเจนจะช่วยให้ Agent ตอบคำถามได้ตรงประเด็นมากขึ้น และลดโอกาสที่ Agent จะตอบเรื่องที่ไม่เกี่ยวข้อง

---

## สรุป

ในแบบฝึกหัดนี้ คุณได้เรียนรู้:
- การเข้าใช้งาน **Copilot Studio** และสร้าง Agent ใหม่
- การกำหนดหน้าที่ให้ Agent ด้วย **ภาษาธรรมดา** (Describe Mode) เพื่อสร้าง HR Agent จาก Prompt
- การกำหนดขอบเขตให้ Agent ช่วยตอบเรื่อง **การเบิกค่าใช้จ่าย** และ **ข่าวสารองค์กร**
- การ **ทดสอบ Agent** เบื้องต้นผ่านหน้าพรีวิว

ขั้นตอนถัดไป → [เพิ่ม Knowledge ให้ Agent](../part2-02-adding-knowledge/README.md)
