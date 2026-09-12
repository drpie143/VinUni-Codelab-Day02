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
| 1 | **Vinhomes** | Tốn thời gian & Pain từ người khác | **Tiếp nhận, phân loại và điều hướng báo cáo phản ánh của cư dân trên App Vinhomes Resident:** Nhân viên CSKH/Ban quản lý phải đọc thủ công hàng trăm phản ánh mỗi ngày (rò rỉ nước, hỏng đèn hành lang, ồn ào, thủ tục phí), phân loại tag sự cố và gán về từng tổ kỹ thuật block tòa nhà, dẫn đến phản hồi ban đầu chậm trễ (từ 2-12 tiếng). |
| 2 | **Vinhomes** | Lặp lại | Soạn thảo câu trả lời và văn bản hướng dẫn thủ tục cư dân (đăng ký thi công nội thất, đăng ký thẻ cư dân/vé gửi xe) lặp đi lặp lại theo biểu mẫu. |
| 3 | **Xanh SM (GSM)** | Tốn thời gian | Điều phối viên xử lý thủ công các báo cáo khẩn cấp từ tài xế về sự cố cạn kiệt pin hoặc tìm trạm sạc trống tương thích gần nhất. |
| 4 | **VinFast** | Lặp lại | Đối chiếu và so khớp dữ liệu hóa đơn sạc điện từ các trạm sạc đối tác công cộng với mức điện năng thực tế của xe theo tuần. |
| 5 | **Vinpearl** | AI-upgrade | Tổng hợp, phân loại cảm xúc (sentiment) và lọc phản ánh tiêu cực từ các kênh OTA (Booking, Agoda, Google Maps) để báo động cho Quản lý khách sạn. |
| 6 | **Vinmec** | Tốn thời gian | Trích xuất và soạn thảo tóm tắt hồ sơ xuất viện (Discharge Summary) từ ghi chú lâm sàng của bác sĩ cho bệnh nhân. |

---

## 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Chọn **top 3 bài toán tiềm năng nhất** để phân tích sơ bộ:

---

### 🎴 Thẻ bài toán #1 (LỰA CHỌN CHÍNH): Vinhomes — Tiếp nhận & Xử lý Báo cáo Phản ánh của Cư dân

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1 (CHOSEN)                                          │
│                                                                         │
│ Bài toán (1 câu): Tiếp nhận, phân loại tự động mức độ khẩn cấp phản ánh │
│ của cư dân trên App Vinhomes Resident, gán đúng đội kỹ thuật & draft SMS.│
│ Công ty thành viên: [ ] VinFast   [ ] Xanh SM   [x] Vinhomes            │
│                                                                         │
│ Ai đang đau (Actor)?                                                    │
│ - Cư dân Vinhomes: Bức xúc khi báo sự cố (chảy nước, mất điện, thang máy)│
│   nhưng phải chờ nhiều giờ mới có phản hồi ban đầu.                     │
│ - Nhân viên CSKH / BQL Tòa nhà: Quá tải đọc hàng nghìn ticket/ngày.     │
│                                                                         │
│ Workflow thủ công hiện tại (5 bước):                                    │
│   1. Cư dân gửi ticket trên App ──> 2. Nhân viên CSKH đọc nội dung      │
│   ──> 3. Phân loại chuyên mục sự cố (Điện/Nước/An ninh/Dịch vụ)         │
│   ──> 4. Gán ticket cho Trưởng nhóm kỹ thuật/an ninh block tòa nhà      │
│   ──> 5. Soạn tin nhắn xác nhận tiếp nhận gửi cư dân                    │
│                                                                         │
│ Bước nào tốn nhất? Bước 2, 3 & 5 (⏱ 12-15 phút/ticket)                  │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3 & 5 (Hiểu ngôn ngữ tự   │
│ nhiên, phân loại tag tự động, phát hiện sự cố khẩn và draft tin nhắn).  │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│ - Giảm thời gian phản hồi ban đầu từ 2-4 tiếng ──> dưới 5 phút.         │
│ - Tỉ lệ phân loại đúng bộ phận phụ trách đạt trên 96%.                  │
│ - 100% sự cố khẩn cấp (cháy nổ, ngập nước nặng) được báo động tức thì. │
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
│                                                                         │
│ Workflow thủ công hiện tại: Nhận tin -> Tra GPS -> Tìm trạm sạc VinFast │
│ -> Soạn tin nhắn chỉ đường -> Gọi xe cứu hộ sạc di động (nếu pin < 5%). │
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
│                                                                         │
│ Workflow thủ công hiện tại: Cư dân hỏi trực tiếp/gọi điện -> Lễ tân tìm │
│ file quy chế nội bộ -> Giải thích bằng lời -> Gửi biểu mẫu qua email.   │
│ Bước tốn nhất: Tra cứu tài liệu và hướng dẫn điền form (15 phút/lượt).  │
│ AI nhảy vào: Chatbot tra cứu RAG sổ tay cư dân và gửi kèm link form.   │
│ Đo thành công: Giải đáp tức thì < 15 giây; giảm 60% cuộc gọi lên lễ tân.│
│ Quick Architecture: [x] LLM Feature (RAG QA)                            │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 🎯 Quyết định lựa chọn bài toán cho Deep-Dive:
Nhóm thống nhất chọn bài toán **"Thẻ #1: Vinhomes — Tiếp nhận & Xử lý Báo cáo Phản ánh của Cư dân"** để thực hiện phân tích sâu (Deep-Dive).

**Lý do lựa chọn:**
1. **Quy mô ảnh hưởng lớn:** Hệ thống đô thị Vinhomes phục vụ hàng trăm nghìn hộ cư dân tại Vinhomes Ocean Park, Smart City, Grand Park... với lượng phản ánh rất lớn mỗi ngày.
2. **Nỗi đau có thật và cấp bách:** Cư dân bức xúc nhất là sự chậm trễ trong khâu tiếp nhận thông tin ban đầu khi xảy ra sự cố kỹ thuật tại căn hộ.
3. **Tính khả thi của AI:** Phù hợp hoàn hảo với năng lực hiểu ngôn ngữ tự nhiên (NLP) tiếng Việt của LLM để phân loại đa nhãn, phát hiện khẩn cấp và draft câu trả lời chuẩn mực dịch vụ Vinhomes, đồng thời có ranh giới con người kiểm duyệt (Human-in-the-loop) rõ ràng.
