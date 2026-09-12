# Lab 02 — Worksheet: AI Product Scoping (Vin Smart Future)

---

## 🏛️ 1. Bối cảnh thực tế: Vin Smart Future (Vingroup)

**Vingroup** — Tập đoàn tư nhân lớn nhất Việt Nam — vừa sáp nhập toàn bộ các phòng ban công nghệ thuộc các công ty thành viên thành một đơn vị công nghệ thống nhất mang tên **Vin Smart Future**. 

Nhiệm vụ của **Vin Smart Future** là xây dựng các giải pháp AI, số hóa, và tự động hóa cốt lõi để nâng cao hiệu suất vận hành và trải nghiệm khách hàng xuyên suốt các công ty thành viên:
* 🚗 **VinFast:** Hệ thống xe điện thông minh (EV), trợ lý AI ảo trong xe, dự đoán bảo trì pin, và quản lý chuỗi cung ứng sản xuất.
* 🚕 **Xanh SM (GSM):** Vận hành đội xe taxi/xe máy điện thông minh, điều vận thông minh (Smart Dispatching), tối ưu hóa lộ trình di chuyển.
* 🏢 **Vinhomes:** Quản lý đô thị thông minh (Smart Cities), trợ lý cư dân thông minh, tối ưu hóa mức tiêu thụ năng lượng.
* 🏥 **Vinmec:** Y tế thông minh, chẩn đoán hình ảnh bằng AI, tối ưu hóa quản lý hồ sơ bệnh án.
* 🎢 **Vinpearl / VinWonders:** Trải nghiệm du lịch số hóa, quản lý phòng và luồng khách thông minh tại các khu vui chơi.

Trong buổi Lab hôm nay, nhóm của bạn sẽ đóng vai trò là **AI Product Engineer** tại **Vin Smart Future**, tiến hành tìm kiếm, scoping, phân tích độ khả thi, thiết lập ranh giới vận hành, và xây dựng một **bản mẫu kỹ thuật (prompt prototype)** cho một bài toán cụ thể thuộc một trong những mảng kinh doanh trên.

---

## 📊 2. Cơ cấu tính điểm bài lab

### 👥 Điểm nhóm (60 điểm)

| Gate | Điểm | Deliverable | Tiêu chí chấm |
|---|---:|---|---|
| **G1. Workflow Mapping** | 20 | Problem Deep-Dive | Vẽ chi tiết quy trình hiện tại: các bước, handoff, thời gian, bottleneck |
| **G2. Problem Statement** | 20 | Problem Deep-Dive | Problem Statement 6-field bám sát thực tế, metric có số và ranh giới rõ ràng |
| **G3. AI Fit & Future Flow** | 10 | Problem Deep-Dive | So sánh Rule vs LLM vs Agent, future flow có bước AI, ranh giới và Fallback |
| **G4. Decision Quality** | 10 | Problem Deep-Dive | Quyết định Go/Not Yet/No-Go trung thực và có chứng cứ rõ ràng |

### 👤 Điểm cá nhân (40 điểm)

| Gate | Điểm | Deliverable | Tiêu chí chấm |
|---|---:|---|---|
| **I1. Scan & Cards** | 15 | Quick Cards | Liệt kê 5 problems sử dụng 3 lenses, hoàn thiện 3 quick cards chất lượng |
| **I2. Prototyping** | 10 | 02-lab/ | Chạy thử nghiệm programmatic prompt prototype thành công |
| **I3. AI Log & Reflection** | 15 | 03-ai-log.md | Phản ánh trung thực về việc dùng AI làm thought-partner (giúp gì, sai gì, sửa gì) |

---

# 🚀 Phase 0 — worked Example: Xanh SM Intelligent Dispatcher (15 min)

*Giảng viên walk-through ví dụ thực tế từ Vin Smart Future để bạn hiểu rõ cách scoping một bài toán AI.*
Đọc chi tiết worked example tại file [02-deliverable-example.md](02-deliverable-example.md).

---

# 🔍 Phase 1 — SCAN (Cá nhân, 20 min)

Hãy sử dụng **4 Lenses** dưới đây để quét qua hoạt động vận hành của các công ty thành viên Vingroup. Ghi lại **ít nhất 5 bài toán/bottleneck** thực tế.

### 4 Lenses tìm bài toán AI cho Vingroup:
1. **Lặp lại (Repetitive):** Tác vụ lặp đi lặp lại nhiều lần hằng ngày. (Ví dụ: So khớp hóa đơn sạc điện tại VinFast, route lại chuyến taxi tại Xanh SM).
2. **Tốn thời gian (Time-consuming):** Tác vụ ngốn thời gian xử lý thủ công của nhân viên. (Ví dụ: Soạn thảo phản hồi đánh giá 1-star của cư dân Vinhomes).
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ khách hàng hiện tại còn chậm hoặc phản hồi rập khuôn. (Ví dụ: Chatbot CSKH Vinpearl hỗ trợ đặt vé vui chơi).
4. **Pain từ người khác (Stakeholder Pain):** Bottleneck khiến khách hàng hoặc nhân viên thực địa phàn nàn. (Ví dụ: Tài xế Xanh SM phàn nàn về việc hệ thống gợi ý điểm đón khách không chính xác).

> [!TIP]
> **🤖 AI Prompts — Partner brainstorm:**
> Hãy sử dụng prompt sau để brainstorm các bài toán thực tế nếu bạn chưa có ý tưởng:
> *"Tôi là AI Engineer tại Vin Smart Future (Vingroup). Tôi đang tìm kiếm các pain point vận hành cụ thể có thể tối ưu bằng AI cho mảng [Chọn một: VinFast / Xanh SM / Vinhomes / Vinmec]. Hãy gợi ý cho tôi 5 quy trình nghiệp vụ thủ công, tốn nhiều thời gian và gây rò rỉ hiệu suất kèm con số thống kê ước tính về tổn thất."*

### 📝 List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | VinFast | Repetitive | So khớp hóa đơn sạc điện tại trạm với dữ liệu phiên sạc (kWh, thời gian, ID xe) để đối soát công nợ — kế toán phải dò tay từng dòng mỗi ngày. |
| 2 | Xanh SM | Stakeholder Pain | Hệ thống gợi ý điểm đón khách không chính xác → tài xế chạy lòng vòng, khách chờ lâu, cả hai đều phàn nàn. |
| 3 | Vinhomes | Time-consuming (Tốn thời gian) | Nhân viên CSKH Ban quản lý mất nhiều thời gian soạn tay phản hồi cho đánh giá 1-sao / phản ánh của cư dân trong app. |
| 4 | Vinpearl / VinWonders | AI-upgrade | Chatbot CSKH đặt vé vui chơi/khách sạn còn rập khuôn, không xử lý được câu hỏi phức tạp và đa ngôn ngữ cho khách quốc tế. |
| 5 | Vinmec | Time-consuming | Bác sĩ/điều dưỡng mất thời gian đọc và tóm tắt bệnh án, tiền sử dài dòng trước mỗi lượt khám. |

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn **top 3 bài toán** từ danh sách trên và hoàn thiện **3 Quick Problem Cards** dưới đây (10 phút/card).

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                        │
│                                                             │
│ Bài toán (1 câu): Tự động trích xuất & so khớp hóa đơn sạc  │
│   điện với log phiên sạc (kWh, thời gian, ID xe) để đối soát │
│   công nợ giữa trạm sạc và đội xe.                           │
│ Công ty thành viên: [x] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Kế toán vận hành / nhân viên đối soát   │
│   công nợ trạm sạc.                                          │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Thu hóa đơn/biên lai sạc ──> 2. Mở log phiên sạc từ hệ │
│   thống ──> 3. Dò tay khớp từng dòng (kWh, giờ, ID) ──>     │
│   4. Đánh dấu sai lệch & lập báo cáo đối soát                │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 (dò khớp tay)        │
│   (⏱ ~3 phút/hóa đơn, 300-500 hóa đơn/ngày)                 │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 1-3: OCR trích    │
│   xuất + entity matching tự động, chỉ đẩy case lệch cho người│
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian đối     │
│   soát mỗi hóa đơn từ 3 min ──> under 20s; tự động khớp ≥90% │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                        │
│                                                             │
│ Bài toán (1 câu): Dự đoán điểm đón khách chính xác hơn dựa  │
│   trên lịch sử + ngữ cảnh (giờ, khu vực, sự kiện) để giảm   │
│   thời gian tài xế tìm khách.                               │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Tài xế Xanh SM & khách hàng đặt xe.    │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Khách đặt xe, ghim điểm đón ──> 2. Hệ thống gán tài xế │
│   ──> 3. Tài xế tới điểm ghim (thường sai/khó tìm) ──>      │
│   4. Gọi điện xác nhận lại vị trí, chạy lòng vòng            │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3-4 (điểm ghim sai)   │
│   (⏱ ~3-5 phút/chuyến hao phí + hủy chuyến)                 │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 1-2: gợi ý điểm  │
│   đón chuẩn từ dữ liệu lịch sử pickup + POI xung quanh       │
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian chờ    │
│   đón trung bình 30% & giảm tỷ lệ hủy chuyến do lệch điểm   │
│   đón từ X% ──> under X/2%                                   │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [ ] LLM  [x] Agent │
└─────────────────────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                        │
│                                                             │
│ Bài toán (1 câu): Sinh bản nháp phản hồi cho đánh giá 1-sao │
│   / phản ánh của cư dân trong app để nhân viên duyệt & gửi. │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên CSKH Ban quản lý toà nhà.   │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Nhận đánh giá/phản ánh 1-sao ──> 2. Đọc & phân loại    │
│   vấn đề ──> 3. Soạn tay phản hồi phù hợp ──> 4. Cấp trên   │
│   duyệt rồi gửi cư dân                                       │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 (soạn tay)          │
│   (⏱ ~10 phút/phản hồi)                                     │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3: phân loại + │
│   sinh bản nháp phản hồi (LLM), luôn có người duyệt (HITL)   │
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian soạn   │
│   phản hồi từ 10 min ──> under 2 min; ≥80% nháp được duyệt  │
│   với chỉnh sửa tối thiểu                                    │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

> [!TIP]
> **🤖 AI Prompts — Stress-Test thẻ bài toán:**
> Hãy dán nội dung thẻ bài toán của bạn vào LLM để nhận phản biện:
> *"Đây là một thẻ bài toán vận hành tôi đề xuất cho Vin Smart Future: [Dán nội dung]. Hãy đóng vai trò là một CFO và Trưởng phòng Vận hành cực kỳ khắt khe, chỉ ra cho tôi 3 điểm yếu về logic, metric, và giải thích vì sao rule-based code thông thường có thể giải quyết bài toán này tốt hơn là dùng AI."*

---

# 🏗️ Phase 3 — DEEP-DIVE (Nhóm, 85 min)

## 3.1. Current-State Workflow Mapping (25 min)
**Vẽ quy trình hiện tại lên bảng/giấy A3.** Sử dụng các ký hiệu:
* 🔴 **Bottleneck:** Bước gây tắc nghẽn, tốn thời gian, hoặc sai sót nhiều nhất.
* 🔄 **Handoff:** Điểm chuyển giao thông tin giữa người và hệ thống, hoặc giữa các bộ phận.
* Ghi rõ thời gian vận hành trung bình: **Tổng cộng = ~18 phút/lượt**.

**Đề tài chọn: Vinhomes Resident Service Copilot** — phân loại/định tuyến khiếu nại của cư dân + trợ lý tra cứu thủ tục, giữ Human-in-the-loop cho nội dung nhạy cảm.

**Current-State Flow (khiếu nại/yêu cầu cư dân qua app Vinhomes):**

```
1. Cư dân gửi yêu cầu/khiếu nại trong app (free-text, đa dạng: điện nước, an ninh,
   phí dịch vụ, thủ tục cư trú...)
        │
        ▼
2. 🔴 Nhân viên trực CSKH đọc, hiểu ý & PHÂN LOẠI thủ công vào đúng nhóm
   (⏱ ~4 phút — dễ phân loại sai khi mô tả mơ hồ)
        │  🔄 Handoff (CSKH → bộ phận chuyên trách)
        ▼
3. 🔴 Định tuyến sang đúng bộ phận (Kỹ thuật / An ninh / Tài chính / BQL)
   — nếu route sai phải chuyển lại (⏱ ~2 phút + rework)
        │
        ▼
4. 🔴 Soạn tay phản hồi hoặc tra cứu thủ tục/biểu phí để trả lời cư dân
   (⏱ ~10 phút — bottleneck nặng nhất, phản hồi rập khuôn)
        │  🔄 Handoff (nhân viên → cấp trên duyệt với case nhạy cảm)
        ▼
5. Cấp trên duyệt (với nội dung nhạy cảm: khiếu nại phí, tranh chấp) → gửi cư dân
   (⏱ ~2 phút)
```

**Tổng cộng ≈ 18 phút/lượt** (chưa tính thời gian chờ trong hàng đợi khi tồn đọng nhiều yêu cầu).

## 3.2. Problem Statement (6-field) & Metrics (15 min)
Điền đầy đủ 6 trường thông tin của bài toán:

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Nhân viên trực CSKH của Ban quản lý Vinhomes (người phân loại, định tuyến & soạn phản hồi cho yêu cầu cư dân trong app). Người thụ hưởng cuối: cư dân. |
| **2. Current Workflow** | Cư dân gửi yêu cầu free-text trong app → CSKH đọc & phân loại thủ công → định tuyến sang đúng bộ phận → tra cứu thủ tục/biểu phí + soạn tay phản hồi → cấp trên duyệt (case nhạy cảm) → gửi. Công cụ: app Vinhomes, file Excel/SharePoint quy trình & biểu phí, email nội bộ. |
| **3. Bottleneck** | (a) Phân loại & định tuyến thủ công dễ sai khi mô tả mơ hồ → rework; (b) Soạn phản hồi / tra cứu thủ tục thủ công ~10 phút/lượt, phản hồi rập khuôn. Đây là các bước cần xử lý ngôn ngữ tự nhiên nhiều nhất. |
| **4. Business Impact** | ~18 phút/lượt × hàng nghìn yêu cầu/tháng trên toàn hệ thống Vinhomes → tồn đọng hàng đợi, vi phạm SLA phản hồi, điểm hài lòng cư dân (CSAT) giảm, chi phí nhân sự CSKH cao. Route sai làm kéo dài thời gian giải quyết. |
| **5. Success Metric** | (1) ≥85% yêu cầu được phân loại & định tuyến đúng dưới 10s; (2) Giảm thời gian soạn phản hồi từ ~10 phút → dưới 2 phút; (3) Giảm tổng thời gian xử lý/lượt từ ~18 phút → dưới 6 phút; (4) ≥80% bản nháp được duyệt với chỉnh sửa tối thiểu. |
| **6. Operational Boundary** | ✅ ĐƯỢC: phân loại intent, gợi ý bộ phận định tuyến, sinh **bản nháp** phản hồi, tra cứu thủ tục/biểu phí từ tài liệu chính thức (RAG). ❌ KHÔNG: tự động gửi phản hồi cho cư dân khi chưa có người duyệt; tự quyết định về tiền/phí, tranh chấp pháp lý, xử lý sự cố an ninh/an toàn khẩn cấp; bịa thông tin thủ tục ngoài tài liệu nguồn. 🟢 CẦN DUYỆT (HITL): mọi nội dung nhạy cảm (khiếu nại phí, tranh chấp, an ninh) bắt buộc con người duyệt trước khi gửi. |

## 3.3. Future-State Flow & AI Fit (25 min)
* **Xác định mức AI Fit (AI-Fit Matrix):** Giải pháp thuộc nhóm nào? [ ] Rule / State-Machine [x] **LLM Feature** [ ] Agentic Loop.
  * **Lý do:** Bài toán chủ yếu là hiểu ngôn ngữ tự nhiên (phân loại intent + sinh nháp phản hồi có RAG), không cần vòng lặp tự hành/nhiều tool phức tạp → LLM Feature là đủ. Có thể nâng lên Agentic sau nếu cần tự tra cứu nhiều nguồn/hành động.

* **So sánh Rule vs LLM vs Agent:**

| Cách tiếp cận | Phù hợp phần nào | Hạn chế |
|---|---|---|
| **Rule / State-Machine** | Định tuyến theo từ khóa cứng ("điện", "nước", "phí") | Gãy khi cư dân mô tả mơ hồ/đa ý/nhiều ngôn ngữ; không soạn được phản hồi tự nhiên |
| **LLM Feature (chọn)** | Phân loại intent linh hoạt + sinh nháp phản hồi + RAG tra thủ tục | Cần kiểm soát ảo giác (bịa thủ tục) → bắt buộc RAG + HITL |
| **Agentic Loop** | Khi cần tự động gọi nhiều tool/hệ thống (đặt lịch kỹ thuật, mở ticket) | Quá mức cần thiết cho MVP; rủi ro & chi phí cao hơn |

* **Vẽ Future-State Flow:**

```
1. Cư dân gửi yêu cầu free-text trong app
        │
        ▼
2. 🔵 AI Step — Phân loại intent + gợi ý bộ phận định tuyến (LLM)
        │        ↩️ Fallback: nếu confidence thấp → gắn nhãn "cần_review" & đẩy hàng đợi CSKH phân loại tay
        ▼
3. 🔵 AI Step — RAG tra cứu thủ tục/biểu phí từ tài liệu chính thức
   + sinh BẢN NHÁP phản hồi (luôn gắn nhãn [DRAFT], không tự gửi)
        │        ↩️ Fallback: nếu không tìm thấy tài liệu nguồn → không bịa, trả "cần nhân viên tra cứu thủ công"
        ▼
4. 🟢 Human Step (HITL) — Nhân viên CSKH review/chỉnh nháp
        │  → Với nội dung nhạy cảm (phí, tranh chấp, an ninh): BẮT BUỘC cấp trên duyệt
        ▼
5. Gửi phản hồi cho cư dân (chỉ sau khi người duyệt) + log để cải thiện model
```

* **Ranh giới & Fallback tóm tắt:**
  * 🟢 **HITL bắt buộc:** không bao giờ tự động gửi; nội dung nhạy cảm phải qua người duyệt.
  * ↩️ **Fallback 1 (phân loại):** confidence thấp → chuyển cho người, không đoán bừa.
  * ↩️ **Fallback 2 (RAG):** thiếu tài liệu nguồn → từ chối bịa, chuyển tra cứu thủ công.

---

# 💻 Phase 4 — TECHNICAL PROMPT PROTOTYPE (Nhóm, 30 min)

Để đảm bảo kỹ sư của Vin Smart Future luôn giữ vững năng lực lập trình, nhóm của bạn sẽ tiến hành **lập trình bản mẫu prompt** trực tiếp trên **Gemini 2.5 Flash** bằng Python để stress-test hệ thống.

### Hướng dẫn thực hiện:
1. Mở file [starter-code/prompt_prototype.py](starter-code/prompt_prototype.py) bằng VS Code/Cursor.
2. Hoàn thiện các nội dung sau:
   * **System Prompt:** Viết chỉ thị cực kỳ nghiêm ngặt quy định vai trò, nhiệm vụ, định dạng output và **Operational Boundary (Ranh giới cấm)** của mô hình.
   * **Structured Output:** Định nghĩa định dạng JSON output rõ ràng.
   * **Adversarial Test Cases:** Viết ít nhất 3 prompts "tấn công" (Adversarial inputs) cố tình dụ AI vượt ranh giới hoặc đưa ra câu trả lời không được phép để kiểm tra xem ranh giới của bạn có thực sự vững chắc.
3. Chạy file python:
   ```bash
   python3 prompt_prototype.py
   ```
4. Kiểm tra xem các ranh giới an toàn có bị LLM phá vỡ hay không và ghi lại kết quả vào worksheet.

---

# 🏁 Phase 5 — EVALUATE (Nhóm, 20 min)

### AI Readiness Checklist:
1. [x] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test? → **Có** — lịch sử yêu cầu/khiếu nại cư dân trong app + kho tài liệu thủ tục/biểu phí chính thức của BQL (dùng làm nguồn RAG). Cần gán nhãn phân loại cho một tập mẫu để đánh giá.
2. [x] Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)? → **Có** — không tự động gửi (bản nháp [DRAFT_ONLY]), nội dung nhạy cảm bắt buộc người duyệt, RAG confidence thấp thì chuyển tra cứu thủ công thay vì bịa.
3. [~] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ? → **Một phần** — CSKH hưởng lợi rõ (giảm tải soạn phản hồi) nhưng cần đào tạo quy trình duyệt nháp; BQL cần chấp nhận việc AI hỗ trợ phân loại.

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
[x] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp.
[ ] **NOT YET (Cần tích lũy thêm dữ liệu/xác lập baseline):** Trì hoãn để chuẩn bị thêm.
[ ] **NO-GO (Không khả thi / Rule-based tốt hơn):** Hủy bỏ dự án AI này.

**Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):**
> **GO với scope hẹp.** Bài toán là xử lý ngôn ngữ tự nhiên đúng thế mạnh của LLM: phân loại intent linh hoạt + sinh nháp phản hồi có RAG — rule-based thuần sẽ gãy khi cư dân mô tả mơ hồ/đa ý. Giá trị đo được rõ: cắt thời gian xử lý từ ~18 phút → dưới 6 phút/lượt, giảm route sai, cải thiện SLA & CSAT.
>
> **Vì sao rủi ro chấp nhận được:** mọi phản hồi đều là bản nháp [DRAFT_ONLY], có Human-in-the-loop; nội dung nhạy cảm (phí, tranh chấp, an ninh) bắt buộc cấp trên duyệt; RAG chống bịa thủ tục, thiếu nguồn thì fallback sang người. Rủi ro sai nằm trong tầm kiểm soát, không tác động trực tiếp tới cư dân khi chưa duyệt.
>
> **Scope hẹp đề xuất cho MVP:** chỉ 1-2 nhóm yêu cầu phổ biến & ít nhạy cảm nhất (VD: hỏi thủ tục/biểu phí, sự cố kỹ thuật điện–nước) tại 1 khu đô thị pilot; đo baseline thủ công trước, rồi so sánh 4 metric ở mục 3.2. Nếu đạt ngưỡng → mở rộng nhóm & khu vực.
>
> **Điều kiện trước khi scale:** gán nhãn tập đánh giá, chốt tài liệu nguồn RAG, và thiết lập vòng phản hồi để cải thiện từ chỉnh sửa của nhân viên.

---

# 📝 Phase 6 — REFLECTION (Cá nhân)
*Ghi nhận phản ánh của cá nhân bạn về việc phối hợp với AI trong buổi học hôm nay vào file `03-ai-log.md`.*
