# แบบฝึกหัดที่ 6: เพิ่ม Tool และ Agent Flow สำหรับส่งขออนุมัติรายงาน

🔑 **ต้องการ M365 Copilot License + สิทธิ์เข้าใช้ Copilot Studio**

แบบฝึกหัดนี้จะต่อยอดจาก `Financial Report Assistant` ตัวเดิม โดยเพิ่มความสามารถที่ใกล้เคียงงานจริงมากขึ้น คือหลังจาก Agent สร้าง draft รายงานการเงินรายเดือนแล้ว ผู้ใช้สามารถสั่งให้ Agent **ส่งคำขออนุมัติ** ไปยังผู้ตรวจสอบได้ผ่าน **Agent Flow** ที่ถูกเพิ่มเข้า Agent เป็น **Tool**

แบบฝึกหัดนี้ตั้งใจให้ scope เล็กแต่ใช้งานได้จริง เพื่อให้ผู้เรียนเห็นภาพว่า Copilot Studio ไม่ได้มีแค่การตอบคำถาม แต่สามารถเชื่อมต่อ workflow ธุรกิจง่ายๆ ได้ด้วย

> ⚠️ **Note:** แบบฝึกหัดนี้คาดหวังว่าอย่างน้อยผู้เรียนได้ทำ Module 3 Exercise 3-4 มาก่อน เพื่อให้มี Topic `Monthly Report Intake` และ output ชื่อ `FinancialAnalysisResult` พร้อมใช้งานแล้ว ส่วน Module 4 Exercise 1 เป็นพื้นฐานเสริมที่ช่วยให้เห็นภาพการขยายความสามารถของ Agent แต่ไม่ใช่ข้อบังคับ

```mermaid
flowchart TD
    A[User ขอวิเคราะห์รายงานการเงิน] --> B[Monthly Report Intake]
    B --> C[Analyze financial data]
    C --> D[Show financial analysis]
    D --> E[Ask Revision]
    E -->|Yes| F[Ask for Additional Prompt]
    F --> G[Update Additional Prompt]
    G --> C
    E -->|No| H[Ask Submit for Approval]
    H -->|No| I[Message: เก็บเป็น draft]
    H -->|Yes| J[Ask Reviewer Email]
    J --> K[Tool: Submit Monthly Report for Approval]
    K --> L[Message: แจ้งผลการส่งขออนุมัติ]
    I --> M[End current topic]
    L --> M
```

```mermaid
flowchart LR
    A[Copilot Studio Topic] --> B[Agent Flow รับข้อมูลรายงาน]
    B --> C[สร้าง Approval request หรือส่งอีเมลขออนุมัติ]
    C --> D[คืนข้อความผลลัพธ์กลับเข้า Agent]
    D --> E[แสดงผลในแชต]
```




---

## Practice 1: ทบทวน flow เดิมและกำหนดเป้าหมายของ action

1. เปิด Agent `Financial Report Assistant` ที่สร้างจาก Module 3
2. ไปที่ Topic `Monthly Report Intake` แล้วทบทวนว่าตอนนี้ flow เดิมทำอะไรได้แล้วบ้าง
   - รับค่า `ReportPeriod`
   - รับค่า `BusinessUnit`
   - รับค่า `ReportFormat`
   - วิเคราะห์ข้อมูลจากไฟล์ Excel
   - แสดงผลลัพธ์ผ่านตัวแปร `FinancialAnalysisResult`
   - รองรับ revision loop จาก feedback ของผู้ใช้
3. ตั้งเป้าหมายของแบบฝึกหัดนี้ให้ชัดว่า หลังจากผู้ใช้พอใจกับ draft แล้ว Agent ต้องถามต่อว่า

   ```text
   ต้องการส่งสรุปรายงานนี้ไปขออนุมัติหรือไม่
   ```

4. ถ้าผู้ใช้ตอบว่าใช่ เราจะให้ Agent เรียก Tool ที่เชื่อมกับ Agent Flow เพื่อส่งข้อมูลไปยังผู้อนุมัติ
5. ในแบบฝึกหัดนี้ให้ใช้ชื่อ Agent Flow ว่า

   ```text
   Submit Monthly Report for Approval
   ```

6. และให้กำหนดผลลัพธ์ปลายทางของ Tool เป็นข้อความสั้นๆ เช่น

   ```text
   Approval request submitted to reviewer@example.com for May 2026 / Aromatics.
   ```

> 💡 **Tip:** ในช่วงแรกยังไม่จำเป็นต้องทำ workflow ซับซ้อน เช่นหลายชั้นอนุมัติหรือเขียนกลับ SharePoint ให้เริ่มจาก action ที่มี input ชัดเจนและส่งข้อความตอบกลับได้ก่อน

---

## Practice 2: สร้าง Agent Flow สำหรับส่งคำขออนุมัติ

1. ไปที่หน้า **Flows** จากเมนูด้านซ้ายของ Copilot Studio แล้วเลือก **New flow** > **Agent flow**
2. ระบบจะเปิด flow designer พร้อม starter template ที่มี 2 action สำคัญมาให้แล้ว คือ

   ```text
   When an agent calls the flow
   Respond to the agent
   ```

3. ตั้งชื่อ flow ว่า

   ```text
   Submit Monthly Report for Approval
   ```

4. ที่ action `When an agent calls the flow` ให้เพิ่ม input parameters อย่างน้อย 5 ค่าเป็นประเภทข้อความดังนี้

   ```text
   ReportPeriod
   BusinessUnit
   ReportFormat
   AnalysisSummary
   ReviewerEmail
   ```

5. ภายใน flow ให้สร้างขั้นตอนธุรกิจแบบง่ายที่สุด 1 อย่างต่อไปนี้ตามสิทธิ์ที่ tenant ของคุณมี
   - สร้าง approval request
   - หรือส่งอีเมลขออนุมัติไปยัง reviewer

6. ที่ action `Respond to the agent` ให้กำหนด output กลับมายัง Agent เพียง 1 ค่า เพื่อให้ง่ายต่อการแสดงผลในแชต เช่น

   ```text
   ApprovalSubmissionResult
   ```

7. ตัวอย่างข้อความ output ที่ flow ควรส่งกลับ:

   ```text
   Approval request submitted to {{ReviewerEmail}} for {{ReportPeriod}} / {{BusinessUnit}} in {{ReportFormat}} format.
   ```

8. ตรวจสอบที่ `Respond to the agent` ว่า flow ถูกตั้งค่าให้ตอบกลับแบบ real-time ไม่ใช่ asynchronous
9. ออกแบบ flow ให้ทำงานจบและตอบกลับ Agent ให้เร็วที่สุด โดยพยายามให้การตอบกลับอยู่ภายในข้อจำกัดเวลาของ action
10. กด **Publish** ให้เรียบร้อยก่อนกลับมาที่ Copilot Studio

> ⚠️ **Note:** Microsoft Learn ระบุว่า flow ที่ใช้เป็น Tool ของ Agent ต้องมี `When an agent calls the flow` และ `Respond to the agent` และควรตอบกลับภายในเวลาที่เหมาะสมสำหรับ action แบบ real-time ดังนั้นในแบบฝึกหัดนี้ให้หลีกเลี่ยง logic ที่ยาวเกินจำเป็นหรือส่งข้อมูลกลับขนาดใหญ่มาก

---

## Practice 3: เพิ่ม Agent Flow เข้า Agent เป็น Tool

1. กลับมาที่หน้า Agent แล้วไปที่ส่วน **Tools**
2. กด **Add a tool** แล้วเลือก flow ที่สร้างไว้ชื่อ `Submit Monthly Report for Approval`
3. ตรวจสอบชื่อและคำอธิบายของ Tool ให้ชัดเจน เช่น

   ```text
   Use this tool when the user confirms that the monthly financial report draft should be sent for approval.
   ```

4. กด **Save** เพื่อให้ Tool นี้พร้อมถูกเรียกใช้จาก Topic
5. ถ้า Agent ของคุณมี instruction ที่อธิบายขอบเขตอยู่แล้ว ให้เพิ่มบรรทัดสั้นๆ เพื่อระบุความสามารถใหม่นี้ เช่น

   ```text
   If the user confirms that a monthly report draft is ready, use Submit Monthly Report for Approval to send an approval request.
   ```

> 💡 **Tip:** ตั้งชื่อ Tool ให้สื่อเจตนาแบบตรงไปตรงมา เพราะชื่อและ description ที่ชัดจะช่วยทั้งตอน authoring และตอนที่ต้องกลับมา review flow ภายหลัง

---

## Practice 4: ต่อ Topic เดิมให้ถามและเรียก Tool

ในแบบฝึกหัดนี้เราจะต่อจากเส้นทาง `Finalize report (No)` ใน Module 3 Exercise 4 โดยยังไม่จบ topic ทันที แต่เพิ่มขั้นตอนขออนุมัติก่อน

1. เปิด Topic `Monthly Report Intake`
2. ไปที่เส้นทางที่ผู้ใช้ตอบ `No` ใน node `Ask Revision`
3. ถ้ายังมี **Message** ที่ใช้ยืนยันผลลัพธ์สุดท้ายอยู่ ให้คงไว้ได้ แต่ยังไม่ต้องต่อไปที่ **End current topic** ทันที
4. เพิ่ม **Question** node ใหม่ใต้เส้นทางนี้ แล้วตั้งค่าดังนี้

   ### Node name
   ```text
   Ask Submit for Approval
   ```

   ### Message
   ```text
   คุณต้องการส่ง draft นี้ไปขออนุมัติหรือไม่?
   ```

   ### Identify
   ```text
   Multiple choice of options
   ```

   ### Options
   ```text
   Yes
   ```
   ```text
   No
   ```

   ### Save user response as
   ```text
   SubmitApprovalAnswer
   ```

5. เพิ่ม **Condition** node เพื่อแยก 2 เส้นทาง
   - `Submit for Approval (Yes)`
   - `Keep as Draft (No)`

6. ในเส้นทาง `Submit for Approval (Yes)` เพิ่ม **Question** node เพื่อรับอีเมลของผู้อนุมัติ

   ### Node name
   ```text
   Ask Reviewer Email
   ```

   ### Message
   ```text
   กรุณาระบุอีเมลของผู้อนุมัติที่ต้องการส่งคำขอ
   ```

   ### Identify
   ```text
   User's entire response
   ```

   ### Save user response as
   ```text
   ReviewerEmail
   ```

7. ต่อจาก `Ask Reviewer Email` ให้เพิ่ม **Tool** node แล้วเลือก Tool `Submit Monthly Report for Approval`
8. map ค่า input ของ Tool ตามนี้

   ```text
   ReportPeriod = Topic.ReportPeriod
   BusinessUnit = Topic.BusinessUnit
   ReportFormat = Topic.ReportFormat
   AnalysisSummary = Topic.FinancialAnalysisResult
   ReviewerEmail = Topic.ReviewerEmail
   ```

9. สร้าง output variable ใหม่สำหรับผลลัพธ์ของ Tool เช่น

   ```text
   ApprovalSubmissionResult
   ```

10. เพิ่ม **Message** node ต่อจาก Tool node เพื่อแสดงผลลัพธ์ที่ส่งกลับมาจาก flow
11. ในข้อความของ Message node ให้แทรกตัวแปร `ApprovalSubmissionResult` เพื่อให้ผู้ใช้เห็นสถานะการส่งคำขออนุมัติในแชตทันที
12. ปิดท้ายเส้นทางนี้ด้วย **End current topic**
13. ส่วนเส้นทาง `Keep as Draft (No)` ให้แสดงข้อความยืนยันว่าเก็บผลลัพธ์ไว้เป็น draft แล้วจึงใช้ **End current topic** ตามเดิม

> ⚠️ **Note:** ถ้าต้องการลดความซับซ้อน ให้เริ่มจากการใช้ `FinancialAnalysisResult` แบบเต็มทั้งก้อนเป็น `AnalysisSummary` ไปก่อน ยังไม่จำเป็นต้องแยกย่อยเป็น KPI หรือ risk ในแบบฝึกหัดนี้

---

## Practice 5: ทดสอบ Happy Path และ Cancel Path

1. เปิด **Test your agent**
2. เริ่มด้วย prompt ตัวอย่างนี้

   ```text
   ช่วยสรุปรายงานการเงินรายเดือนของ BU Aromatics เดือน May
   ```

3. ตอบค่าระหว่างทางให้ครบจนได้ผลวิเคราะห์จาก `FinancialAnalysisResult`
4. เมื่อระบบถามเรื่อง revision ให้ตอบ

   ```text
   No
   ```

5. เมื่อระบบถามว่าต้องการส่งขออนุมัติหรือไม่ ให้ทดสอบ **Happy Path** โดยตอบ

   ```text
   Yes
   ```

6. ใส่อีเมล reviewer เช่น

   ```text
   finance-manager@set.example
   ```

7. ตรวจว่าระบบเรียก Tool สำเร็จและมีข้อความยืนยันกลับมา เช่น

   ```text
   Approval request submitted to finance-manager@set.example for May / Aromatics.
   ```

8. จากนั้นทดสอบ **Cancel Path** อีกรอบ โดยเริ่ม flow เดิมใหม่ แต่ที่คำถาม `Ask Submit for Approval` ให้ตอบ

   ```text
   No
   ```

9. Expected result ของเส้นทางนี้คือ
   - ระบบไม่เรียก Tool
   - ระบบแจ้งว่าเก็บ draft ไว้เรียบร้อยแล้ว
   - Topic จบอย่างปลอดภัย

10. ถ้าระบบเรียก Tool ไม่สำเร็จ ให้ตรวจ 3 จุดนี้
   - flow ถูก publish แล้วหรือยัง
   - Tool ถูกเพิ่มเข้า Agent แล้วหรือยัง
   - input mapping ใช้ชื่อตัวแปรจาก Topic ถูกต้องหรือไม่

> 💡 **Tip:** ถ้ามีเวลา ให้เปิดตัวติดตาม topic หรือดูค่าตัวแปรใน test panel เพื่อเช็กว่า `ReviewerEmail` และ `ApprovalSubmissionResult` ถูกส่งผ่าน flow ตามที่ตั้งใจไว้จริง

---

## สรุป

ในแบบฝึกหัดนี้ คุณได้เพิ่มความสามารถเชิงธุรกิจให้ Agent เดิมด้วย Tool และ Agent Flow สำหรับส่งคำขออนุมัติหลังจากสร้าง draft รายงานเสร็จ ทำให้ Agent ไม่ได้แค่วิเคราะห์ข้อมูล แต่สามารถเริ่ม workflow ที่ใช้งานจริงต่อได้ทันที

อ่านเพิ่มเติมได้ที่:
- [Microsoft Learn: Create an agent flow as a tool](https://learn.microsoft.com/en-us/microsoft-copilot-studio/advanced-flow-create)
- [Microsoft Learn: Create and edit topics](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-create-edit-topics)
- [Microsoft Learn: Ask a question](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-ask-a-question)

ขั้นตอนถัดไป → [ออกแบบ Fallback และ Mini Test Cycle](../../module-5/exercise-1-fallback-and-mini-test/README.md)