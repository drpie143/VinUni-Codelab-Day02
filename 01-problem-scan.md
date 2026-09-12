# 01 — Problem Scan & Quick Problem Cards (Vin Smart Future)

**Học viên:** Quang Dũng  
**Đơn vị:** Vin Smart Future (Vingroup)  
**Mảng trọng tâm:** Đô thị Thông minh & Quản lý Dịch vụ Cư dân (Vinhomes) — Hợp tác liên đơn vị (VinFast, Xanh SM)

---

## 🔍 Phase 1 — SCAN: Bảng quét cơ hội (4 Lenses)

Quét qua các hoạt động vận hành thực tế tại các công ty thành viên Vingroup dựa trên **4 Lenses**:
1. **Lặp lại (Repetitive):** Tác vụ lặp đi lặp lại nhiều lần trong ngày với quy trình tương tự.
2. **Tốn thời gian (Time-consuming):** Tác vụ ngốn nhiều giờ xử lý thủ công của đội ngũ vận hành.
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ khách hàng/cư dân còn rập khuôn, xử lý ngôn ngữ tự nhiên còn chậm.
4. **Pain từ người khác (Stakeholder Pain):** Điểm nghẽn gây bức xúc, phàn nàn kéo dài từ cư dân hoặc nhân viên thực địa.

### Danh sách các bài toán phát hiện:

| # | Công ty thành viên | Lens áp dụng | Mô tả bài toán & Bottleneck thực tế |
|---|--------------------|--------------|--------------------------------------|
| 1 | **Vinhomes** | Tốn thời gian & Pain từ người khác | **Vinhomes Resident Service Copilot (Tiếp nhận, phân loại kép và điều phối phản ánh cư dân):** Nhân viên CSKH/Ban quản lý phải đọc thủ công hàng nghìn phản ánh mỗi ngày, vừa phải phân loại các sự cố kỹ thuật (điện, nước, thang máy, an ninh) để gán thợ, vừa phải tra cứu thủ tục (thi công, thẻ xe, biểu mẫu) để giải thích cho cư dân, dẫn đến thời gian chờ phản hồi kéo dài từ 2 đến 12 tiếng. |
| 2 | **Vinhomes** | Lặp lại | Soạn thảo câu trả lời và văn bản hướng dẫn thủ tục cư dân (đăng ký thi công nội thất, đăng ký thẻ cư dân/vé gửi xe) lặp đi lặp lại theo biểu mẫu. |
| 3 | **Xanh SM (GSM)** | Tốn thời gian | Điều phối viên xử lý thủ công các báo cáo khẩn cấp từ tài xế về sự cố cạn kiệt pin hoặc tìm trạm sạc trống tương thích gần nhất. |
| 4 | **VinFast** | Lặp lại | Đối chiếu và so khớp dữ liệu hóa đơn sạc điện từ các trạm sạc đối tác công cộng với mức điện năng thực tế của xe theo tuần. |
| 5 | **Vinpearl** | AI-upgrade | Tổng hợp, phân loại cảm xúc (sentiment) và lọc phản ánh tiêu cực từ các kênh OTA (Booking, Agoda, Google Maps) để báo động cho Quản lý khách sạn. |
| 6 | **Vinmec** | Tốn thời gian | Trích xuất và soạn thảo tóm tắt hồ sơ xuất viện (Discharge Summary) từ ghi chú lâm sàng của bác sĩ cho bệnh nhân. |

---

## 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Chọn **top 3 bài toán tiềm năng nhất** để phân tích sơ bộ:

---

### 🎴 Thẻ bài toán #1 (LỰA CHỌN CHÍNH): Vinhomes Resident Service Copilot

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1 (CHOSEN) — ĐỒNG BỘ VỚI WORKFLOW DIAGRAM           │
│                                                                         │
│ Bài toán (1 câu): Vinhomes Resident Service Copilot — Hệ thống AI hỗ trợ│
│ CSKH tự động phân loại kép (Khiếu nại kỹ thuật vs Thủ tục hành chính),  │
│ điều phối KTV và soạn nháp tin nhắn [DRAFT_ONLY] gửi cư dân.            │
│ Công ty thành viên: [ ] VinFast   [ ] Xanh SM   [x] Vinhomes            │
│                                                                         │
│ Ai đang đau (Actor)?                                                    │
│ - Cư dân Vinhomes: Bức xúc khi báo sự cố (chảy nước, mất điện, thang máy)│
│   hoặc hỏi thủ tục thi công nhưng phải chờ nhiều giờ mới có phản hồi.   │
│ - Nhân viên CSKH / BQL Tòa nhà: Quá tải đọc và gõ tay hàng nghìn ticket.│
│                                                                         │
│ Workflow thủ công hiện tại (5 bước đồng bộ sơ đồ 04):                   │
│   1. Tiếp nhận đa kênh (App Vinhomes Resident, Hotline, Web Portal)     │
│   ──> 2. [🔴 BOTTLENECK] Đọc & Phân loại kép (Sự cố vs Thủ tục)         │
│   ──> 3. [🔄 3x HANDOFFS] Tra cứu KTV tòa nhà HOẶC Tra Sổ tay Cư dân    │
│   ──> 4. [🔴 BOTTLENECK] Soạn thảo tin nhắn phản hồi chuẩn Vinhomes 5 sao│
│   ──> 5. [🚨 EMERGENCY] Kích hoạt điều phối cứu hộ khẩn cấp (< 30s)     │
│                                                                         │
│ Bước nào tốn nhất? Bước 2 & 4 (⏱ 12/18 phút/ticket - chiếm 67% thời gian)│
│ AI có thể nhảy vào cải tiến ở đâu?                                      │
│ - Bước 1: Trích xuất tự động metadata căn hộ, ảnh lỗi hiện trường.      │
│ - Bước 2: Phân luồng kép (Dual-Stream) + Phát hiện cảm xúc bức xúc.     │
│ - Bước 3: Tự động hóa điểm chuyển giao (Seamless Handoff) qua API KTV   │
│   và RAG tra cứu chính xác điều khoản Sổ tay Cư dân.                    │
│ - Bước 4: Soạn sẵn tin nháp [DRAFT_ONLY] cho CSKH 1-click phê duyệt.    │
│ - Bước 5: Bypass hàng chờ thông thường, kích hoạt chuông báo động 24/7.  │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│ - Giảm thời gian phản hồi ban đầu từ 2-4 tiếng ──> dưới 2 phút.         │
│ - Tỉ lệ phân loại đúng chuyên mục và đúng KTV đạt >= 96%.               │
│ - 100% sự cố khẩn cấp (cháy nổ, ngập nước, kẹt thang) báo động < 30s.   │
│                                                                         │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM Feature  [ ] Agent     │
└─────────────────────────────────────────────────────────────────────────┘
```

---

### 🎴 Thẻ bài toán #2: Xanh SM — Điều phối sự cố sạc pin và cứu hộ di động

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                   │
│                                                                         │
│ Bài toán (1 câu): Hỗ trợ điều phối viên Xanh SM lọc trạm sạc trống và    │
│ kích hoạt xe sạc cứu hộ di động khi xe cạn kiệt pin.                    │
│ Công ty thành viên: [ ] VinFast   [x] Xanh SM   [ ] Vinhomes            │
│                                                                         │
│ Ai đang đau (Actor)? Tài xế (lo lắng hết pin giữa đường), Điều phối viên│
│ Workflow thủ công: Nhận tin -> Tra GPS -> Tìm trạm sạc -> Soạn tin nhắn │
│ -> Kích hoạt xe sạc cứu hộ di động (nếu pin < 5%).                      │
│ Bước tốn nhất: Tra cứu trạm và soạn tin (10 phút/lượt).                 │
│ AI nhảy vào: Tự động lọc trạm và draft tin nhắn chuẩn an toàn.          │
│ Đo thành công: Xử lý sự cố dưới 3 phút; 100% an toàn pin < 5%.          │
│ Quick Architecture: [x] LLM Feature (Co-pilot)                          │
└─────────────────────────────────────────────────────────────────────────┘
```

---

### 🎴 Thẻ bài toán #3: Vinhomes — Trợ lý ảo giải đáp thủ tục cư dân 24/7

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                                   │
│                                                                         │
│ Bài toán (1 câu): Giải đáp tự động các câu hỏi về thủ tục đăng ký thi   │
│ công nội thất, cấp thẻ cư dân và nội quy khu đô thị Vinhomes.           │
│ Công ty thành viên: [ ] VinFast   [ ] Xanh SM   [x] Vinhomes            │
│                                                                         │
│ Ai đang đau (Actor)? Cư dân mới dọn về, Nhân viên lễ tân sảnh văn phòng.│
│ Workflow thủ công: Cư dân hỏi -> Lễ tân tìm file quy chế -> Hướng dẫn   │
│ -> Gửi biểu mẫu qua email (mất 15 phút/lượt).                           │
│ Bước tốn nhất: Tra cứu tài liệu và giải thích form đăng ký.             │
│ AI nhảy vào: Chatbot tra cứu RAG sổ tay cư dân và gửi kèm link form.   │
│ Đo thành công: Giải đáp tức thì < 15 giây; giảm 60% cuộc gọi lên lễ tân.│
│ Quick Architecture: [x] LLM Feature (RAG QA)                            │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 🎯 Quyết định lựa chọn bài toán cho Deep-Dive:
Nhóm thống nhất chọn bài toán **"Thẻ #1: Vinhomes Resident Service Copilot — Kết hợp phân loại/chuyển khiếu nại và trợ lý thủ tục"** để thực hiện phân tích sâu (Deep-Dive).

**Lý do lựa chọn hoàn toàn bám sát tiêu chí:**
1. **Gần với nhu cầu thực tế nhóm quan tâm:** Hệ sinh thái đô thị Vinhomes với hàng triệu cư dân là môi trường lý tưởng để AI tạo ra tác động xã hội to lớn.
2. **Lượng yêu cầu lặp lại lớn:** Hàng nghìn phản ánh mỗi ngày giúp mô hình AI có cơ hội tối ưu hóa liên tục.
3. **Dễ dàng đo lường:** Thời gian xử lý thủ công giảm từ 18 phút xuống dưới 2 phút, đo đếm định lượng minh bạch.
4. **Human-in-the-loop vững chắc:** Với các khiếu nại nhạy cảm hoặc nguy cơ tranh chấp, luôn có nhân viên CSKH duyệt tin trước khi gửi qua tiền tố `[DRAFT_ONLY]`.
