# 02 — Deep-Dive Report: Vinhomes Resident Service Copilot

*Lab 02 — AI Product Scoping (Vin Smart Future). Nội dung Phase 3 (DEEP-DIVE) + Phase 5 (EVALUATE).*

**Đề tài:** Vinhomes Resident Service Copilot — phân loại/định tuyến khiếu nại của cư dân + trợ lý tra cứu thủ tục, giữ Human-in-the-loop cho nội dung nhạy cảm.

---

## 3.1. Current-State Workflow Mapping

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
5. Cấp trên duyệt (nội dung nhạy cảm: khiếu nại phí, tranh chấp) → gửi cư dân
   (⏱ ~2 phút)
```

**Tổng cộng ≈ 18 phút/lượt** (chưa tính thời gian chờ trong hàng đợi khi tồn đọng nhiều yêu cầu).

> Sơ đồ trực quan hóa: xem file `04-workflow-diagram.png` (nguồn vẽ tại `04-workflow-diagram.mmd`).

---

## 3.2. Problem Statement (6-field) & Metrics

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Nhân viên trực CSKH của Ban quản lý Vinhomes (phân loại, định tuyến & soạn phản hồi cho yêu cầu cư dân trong app). Người thụ hưởng cuối: cư dân. |
| **2. Current Workflow** | Cư dân gửi yêu cầu free-text → CSKH đọc & phân loại thủ công → định tuyến sang đúng bộ phận → tra cứu thủ tục/biểu phí + soạn tay phản hồi → cấp trên duyệt (case nhạy cảm) → gửi. Công cụ: app Vinhomes, file Excel/SharePoint quy trình & biểu phí, email nội bộ. |
| **3. Bottleneck** | (a) Phân loại & định tuyến thủ công dễ sai khi mô tả mơ hồ → rework; (b) Soạn phản hồi / tra cứu thủ tục thủ công ~10 phút/lượt, phản hồi rập khuôn. Đây là các bước cần xử lý ngôn ngữ tự nhiên nhiều nhất. |
| **4. Business Impact** | ~18 phút/lượt × hàng nghìn yêu cầu/tháng trên toàn hệ thống Vinhomes → tồn đọng hàng đợi, vi phạm SLA phản hồi, điểm hài lòng cư dân (CSAT) giảm, chi phí nhân sự CSKH cao. Route sai làm kéo dài thời gian giải quyết. |
| **5. Success Metric** | (1) ≥85% yêu cầu được phân loại & định tuyến đúng dưới 10s; (2) Giảm thời gian soạn phản hồi từ ~10 phút → dưới 2 phút; (3) Giảm tổng thời gian xử lý/lượt từ ~18 phút → dưới 6 phút; (4) ≥80% bản nháp được duyệt với chỉnh sửa tối thiểu. |
| **6. Operational Boundary** | ✅ ĐƯỢC: phân loại intent, gợi ý bộ phận định tuyến, sinh **bản nháp** phản hồi, tra cứu thủ tục/biểu phí từ tài liệu chính thức (RAG). ❌ KHÔNG: tự động gửi phản hồi cho cư dân khi chưa có người duyệt; tự quyết định về tiền/phí, tranh chấp pháp lý, xử lý sự cố an ninh/an toàn khẩn cấp; bịa thông tin thủ tục ngoài tài liệu nguồn. 🟢 CẦN DUYỆT (HITL): mọi nội dung nhạy cảm (khiếu nại phí, tranh chấp, an ninh) bắt buộc con người duyệt trước khi gửi. |

---

## 3.3. Future-State Flow & AI Fit

**AI-Fit Matrix:** ☑ **LLM Feature** (không phải Rule thuần, chưa cần Agentic Loop)

**Lý do:** Bài toán chủ yếu là hiểu ngôn ngữ tự nhiên (phân loại intent + sinh nháp phản hồi có RAG), không cần vòng lặp tự hành/nhiều tool phức tạp → LLM Feature là đủ. Có thể nâng lên Agentic sau nếu cần tự động gọi nhiều hệ thống.

**So sánh Rule vs LLM vs Agent:**

| Cách tiếp cận | Phù hợp phần nào | Hạn chế |
|---|---|---|
| **Rule / State-Machine** | Định tuyến theo từ khóa cứng ("điện", "nước", "phí") | Gãy khi cư dân mô tả mơ hồ/đa ý/nhiều ngôn ngữ; không soạn được phản hồi tự nhiên |
| **LLM Feature (chọn)** | Phân loại intent linh hoạt + sinh nháp phản hồi + RAG tra thủ tục | Cần kiểm soát ảo giác (bịa thủ tục) → bắt buộc RAG + HITL |
| **Agentic Loop** | Khi cần tự động gọi nhiều tool/hệ thống (đặt lịch kỹ thuật, mở ticket) | Quá mức cần thiết cho MVP; rủi ro & chi phí cao hơn |

**Future-State Flow:**

```
1. Cư dân gửi yêu cầu free-text trong app
        │
        ▼
2. 🔵 AI Step — Phân loại intent + gợi ý bộ phận định tuyến (LLM)
        │        ↩️ Fallback: confidence thấp → gắn nhãn "cần_review" & đẩy hàng đợi CSKH phân loại tay
        ▼
3. 🔵 AI Step — RAG tra cứu thủ tục/biểu phí từ tài liệu chính thức
   + sinh BẢN NHÁP phản hồi (luôn gắn nhãn [DRAFT_ONLY], không tự gửi)
        │        ↩️ Fallback: không tìm thấy tài liệu nguồn → không bịa, trả "cần nhân viên tra cứu thủ công"
        ▼
4. 🟢 Human Step (HITL) — Nhân viên CSKH review/chỉnh nháp
        │  → Nội dung nhạy cảm (phí, tranh chấp, an ninh): BẮT BUỘC cấp trên duyệt
        ▼
5. Gửi phản hồi cho cư dân (chỉ sau khi người duyệt) + log để cải thiện model
```

**Ranh giới & Fallback tóm tắt:**
- 🟢 **HITL bắt buộc:** không bao giờ tự động gửi; nội dung nhạy cảm phải qua người duyệt.
- ↩️ **Fallback 1 (phân loại):** confidence thấp → chuyển cho người, không đoán bừa.
- ↩️ **Fallback 2 (RAG):** thiếu tài liệu nguồn → từ chối bịa, chuyển tra cứu thủ công.

---

## Phase 5 — EVALUATE

### AI Readiness Checklist
1. ☑ **Có dữ liệu mẫu/logs sạch để test?** → Có — lịch sử yêu cầu/khiếu nại cư dân trong app + kho tài liệu thủ tục/biểu phí chính thức của BQL (nguồn RAG). Cần gán nhãn phân loại cho một tập mẫu để đánh giá.
2. ☑ **Rủi ro khi AI sai có nằm trong tầm kiểm soát?** → Có — không tự động gửi (bản nháp [DRAFT_ONLY]), nội dung nhạy cảm bắt buộc người duyệt, RAG confidence thấp thì chuyển tra cứu thủ công thay vì bịa.
3. 〰️ **Stakeholders sẵn sàng thay đổi quy trình cũ?** → Một phần — CSKH hưởng lợi rõ (giảm tải soạn phản hồi) nhưng cần đào tạo quy trình duyệt nháp; BQL cần chấp nhận AI hỗ trợ phân loại.

### Quyết định: ✅ **GO (với scope hẹp)**

**Justification:**
- **GO với scope hẹp.** Bài toán là xử lý ngôn ngữ tự nhiên đúng thế mạnh của LLM: phân loại intent linh hoạt + sinh nháp phản hồi có RAG — rule-based thuần sẽ gãy khi cư dân mô tả mơ hồ/đa ý. Giá trị đo được rõ: cắt thời gian xử lý từ ~18 phút → dưới 6 phút/lượt, giảm route sai, cải thiện SLA & CSAT.
- **Vì sao rủi ro chấp nhận được:** mọi phản hồi đều là bản nháp [DRAFT_ONLY], có Human-in-the-loop; nội dung nhạy cảm (phí, tranh chấp, an ninh) bắt buộc cấp trên duyệt; RAG chống bịa thủ tục, thiếu nguồn thì fallback sang người. Rủi ro sai nằm trong tầm kiểm soát, không tác động trực tiếp tới cư dân khi chưa duyệt.
- **Scope hẹp đề xuất cho MVP:** chỉ 1–2 nhóm yêu cầu phổ biến & ít nhạy cảm nhất (VD: hỏi thủ tục/biểu phí, sự cố kỹ thuật điện–nước) tại 1 khu đô thị pilot; đo baseline thủ công trước, rồi so sánh 4 metric ở mục 3.2. Nếu đạt ngưỡng → mở rộng nhóm & khu vực.
- **Điều kiện trước khi scale:** gán nhãn tập đánh giá, chốt tài liệu nguồn RAG, và thiết lập vòng phản hồi để cải thiện từ chỉnh sửa của nhân viên.
