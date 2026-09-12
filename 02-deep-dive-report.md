# 02 — Problem Deep-Dive Report: Vin Smart Future

**Dự án:** Vinhomes Resident Service Copilot — Hệ thống AI Tiếp nhận, Phân loại kép & Điều phối Dịch vụ Cư dân  
**Đơn vị áp dụng:** Vinhomes — Phối hợp cùng Vin Smart Future  
**Học viên thực hiện:** Quang Dũng

---

## 🏛️ 1. Bối cảnh & Vai trò

Nhóm chúng tôi được giao trọng trách nghiên cứu giải pháp AI giúp nâng cao chất lượng vận hành dịch vụ đô thị thông minh cho **Vinhomes**.

Khảo sát thực địa tại Ban Quản lý các đại đô thị Vinhomes (Vinhomes Ocean Park, Smart City, Grand Park), mỗi ngày hệ thống ứng dụng **Vinhomes Resident** tiếp nhận từ 1,500 đến 2,500 phản ánh và yêu cầu từ cư dân. Đội ngũ Chăm sóc Cư dân (CSKH) đang đối mặt với một áp lực khổng lồ khi phải giải quyết đồng thời hai nhóm việc hoàn toàn khác nhau:
1. **Xử lý sự cố kỹ thuật / Khiếu nại thực địa:** Rò rỉ nước, chập điện, kẹt thang máy, đỗ xe bừa bãi, làm ồn ban đêm (cần điều thợ kỹ thuật/bảo vệ chạy tới căn hộ).
2. **Giải đáp & Hướng dẫn thủ tục hành chính:** Đăng ký thi công nội thất, cấp thẻ cư dân, đăng ký vé xe tháng, giải thích biểu phí quản lý (cần lật tìm Sổ tay Cư dân và gửi form đăng ký).

Hiện tại, toàn bộ quy trình này được vận hành thủ công, dẫn đến độ trễ phản hồi ban đầu kéo dài từ 2 đến 12 tiếng vào giờ cao điểm, gây bức xúc cho cư dân và làm quá tải nhân sự trực ban.

---

## 🏗️ 2. Phase 3 — DEEP-DIVE

### 3.1. Current-State Workflow Mapping (Đồng bộ 100% với Sơ đồ 04)

Quy trình vận hành thủ công hiện tại gồm 5 bước tuần tự như sau:

```text
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ BƯỚC 01         │       │ BƯỚC 02         │       │ BƯỚC 03         │       │ BƯỚC 04         │
│ Tiếp nhận đa    │       │ Đọc & Phân loại │       │ Tra cứu &       │       │ Soạn tin &      │
│ kênh (App/Call) │ ────> │ kép (Sự cố vs   │ ────> │ Điều phối       │ ────> │ Phản hồi cư dân │
│                 │  🔄   │ Thủ tục)        │  🔄   │ (KTV / Sổ tay)  │  🔄   │                 │
│ Actor: Cư dân   │       │ Actor: CSKH     │       │ Actor: CSKH     │       │ Actor: CSKH     │
│ Thời gian: 2 phút       │ Thời gian: 6 phút 🔴    │ Thời gian: 4 phút       │ Thời gian: 6 phút 🔴
│ In: Text / Ảnh lỗi      │ In: Đọc hiểu văn bản    │ In: Danh bạ / Quy chế   │ In: Mẫu phản hồi
│ Out: Ticket thô │       │ Out: Tag & SLA  │       │ Out: Gán KTV / Form     │ Out: SMS / App Driver
└─────────────────┘       └─────────────────┘       └─────────────────┘       └─────────────────┘
                                   │
                                   │ (Nếu phát hiện nguy cơ khẩn cấp)
                                   ▼
                          ┌─────────────────────────────────────────────────────────────┐
                          │ BƯỚC 05: ĐIỀU PHỐI CỨU HỘ KHẨN CẤP (🚨 EMERGENCY DISPATCH)   │
                          │ Actor: Hotline BQL & Kỹ thuật trưởng 24/7  |  ⏱ < 30 GIÂY    │
                          │ Out: Còi báo động, điều xe cứu hộ kỹ thuật, sơ tán khẩn cấp │
                          └─────────────────────────────────────────────────────────────┘

Ký hiệu:
- 🔴 Bottleneck: Bước 02 & Bước 04 chiếm 12/18 phút (67% tổng thời gian xử lý ban đầu).
- 🔄 3x Handoffs: Chuyển giao dữ liệu thủ công giữa Cư dân -> CSKH -> Danh bạ KTV / Sổ tay -> KTV hiện trường.
- ⏱ Tổng thời gian thao tác thủ công: 18 phút/ticket. Thời gian chờ thực tế: 2 - 12 tiếng.
```

---

### 🔍 3.2. Bảng Phân Tích & Đề Xuất Cải Tiến AI Cho Từng Bước Quy Trình

Dưới đây là phương án cải tiến chi tiết từng bước nhằm xóa bỏ điểm nghẽn và tự động hóa các điểm chuyển giao (Handoffs):

| Bước trong Quy trình | Thực trạng thủ công (Current-State) & Điểm yếu | Giải pháp cải tiến bằng AI (AI Enhancement) | Tác động vận hành (Business Impact) |
| :--- | :--- | :--- | :--- |
| **BƯỚC 01: Tiếp nhận đa kênh** | Cư dân gửi text tự do, ảnh lỗi rời rạc trên App/Zalo/Hotline. Dữ liệu chưa chuẩn hóa, nhân viên phải hỏi đi hỏi lại mã căn hộ và block nhà. | **Multimodal Input & Auto-Context:**<br>- Tự động trích xuất mã căn hộ, tầng, block từ tài khoản đăng nhập App.<br>- Vision AI tự động nhận diện ảnh chụp (ví dụ: phát hiện vết nứt tường, vỡ đường ống nước, xe đỗ sai vạch).<br>- Speech-to-Text chuyển đổi tin nhắn thoại của cư dân thành văn bản. | Cư dân không phải nhập lại thông tin cá nhân. Chuẩn hóa 100% dữ liệu đầu vào ngay từ giây đầu tiên. |
| **BƯỚC 02: Đọc & Phân loại kép (🔴 Bottleneck)** | Nhân viên CSKH đọc thủ công (mất 6 phút), dễ gán nhầm chuyên mục do cư dân viết tắt hoặc dùng tiếng lóng. Không phân biệt được tính cấp bách. | **Dual-Stream NLP Classifier & Sentiment Analysis:**<br>- **Luồng A (Sự cố):** Phân loại cây sự cố (Điện, Nước, Thang máy, An ninh) kèm cấp độ khẩn cấp (SLA 1 - 4).<br>- **Luồng B (Thủ tục):** Nhận diện loại thủ tục (Thi công, làm thẻ xe, đăng ký thang máy hàng).<br>- **Phân tích cảm xúc:** Nhận diện cư dân đang bức xúc để gắn cờ ưu tiên chăm sóc đặc biệt. | Rút ngắn thời gian đọc và phân loại từ 6 phút xuống **dưới 3 giây**. Độ chính xác phân loại đạt **$\ge 96\%$**. |
| **BƯỚC 03: Tra cứu & Điều phối (🔄 Handoffs)** | CSKH mở file Excel tìm xem thợ nào trực block nhà đó, hoặc lật Sổ tay Cư dân tìm file biểu mẫu (mất 4 phút qua 3 màn hình rời rạc). Dễ gán nhầm thợ. | **Automated Dispatching API & RAG Knowledge Retrieval:**<br>- **Nếu Sự cố:** API tự động đối chiếu lịch trực KTV thời gian thực $\rightarrow$ gán thẳng ticket cho KTV đang rảnh nhất ở block đó.<br>- **Nếu Thủ tục:** RAG (Retrieval-Augmented Generation) tra cứu chính xác điều khoản trong Sổ tay Cư dân và tự động gắp link biểu mẫu đăng ký online. | Xóa bỏ hoàn toàn 3 điểm Handoff thủ công. Giảm thời gian điều chuyển từ 4 phút xuống **tức thì (< 2s)**. |
| **BƯỚC 04: Soạn tin & Phản hồi (🔴 Bottleneck)** | CSKH gõ tay từng câu trả lời cho cư dân (mất 6 phút), văn phong không đồng bộ, dễ sai sót thông tin cam kết hoặc quên đính kèm hướng dẫn. | **Generative Response Draft & 1-Click HITL Approval:**<br>- AI soạn sẵn tin nhắn phản hồi chuẩn văn phong Vinhomes 5 sao mang tiền tố `[DRAFT_ONLY]`.<br>- Tự động điền thời gian dự kiến thợ đến hoặc tóm tắt 3 bước làm thủ tục kèm form tải.<br>- **Human-in-the-loop:** Nhân viên CSKH chỉ cần 10 giây đọc lướt và bấm nút Duyệt gửi. | Rút ngắn thời gian soạn tin từ 6 phút xuống **dưới 15 giây**. Đảm bảo chuẩn mực dịch vụ cao cấp, không lo AI phát ngôn bừa bãi. |
| **BƯỚC 05: Điều phối cứu hộ khẩn cấp** | Các tin báo khẩn cấp (mùi khét, vỡ ống nước ngập sàn, kẹt thang máy) bị lẫn trong hàng nghìn ticket thường, có nguy cơ bị chậm trễ gây thảm họa. | **Zero-Latency Priority Bypass Guardrail:**<br>- AI kích hoạt rule an toàn tức thì khi phát hiện từ khóa nguy cấp.<br>- Bỏ qua hàng chờ thông thường, rung chuông báo động tại Phòng Điều khiển Trung tâm và tự động gọi hotline Kỹ thuật trưởng 24/7 trong **< 30 giây**. | Giảm thiểu 100% rủi ro thiệt hại về người và tài sản. Phản ứng khẩn cấp tức thì. |

---

### 3.3. Problem Statement (6-field) — Chuẩn Vin Smart Future

| Trường thông tin | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Nhân viên Chăm sóc Cư dân (CSKH) và Trưởng ca Quản lý Vận hành Tòa nhà tại các Khu Đô thị Vinhomes. |
| **2. Current Workflow** | Cư dân tạo phản ánh trên App Vinhomes Resident. Nhân viên CSKH đọc nội dung, tự phân loại thành Sự cố kỹ thuật hoặc Hỏi đáp thủ tục, mở phần mềm tra cứu KTV hoặc mở Sổ tay Cư dân, sau đó tự gõ câu trả lời tiếp nhận gửi lại cho cư dân. Toàn bộ qua 5 bước thủ công, tốn 18 phút/ticket và gây trễ 2 - 12 tiếng khi quá tải. |
| **3. Bottleneck** | **Bước 02 & Bước 04 (chiếm 67% thời gian):** Đọc hiểu phân loại kép và gõ tay soạn thảo văn bản phản hồi cá nhân hóa đúng chuẩn mực 5 sao của Vinhomes. |
| **4. Business Impact** | Mỗi đại đô thị tiếp nhận 1,500 - 2,500 tickets/tuần. Thời gian chờ lâu khiến tỷ lệ khiếu nại leo thang tăng 25%, quá tải tổng đài cuộc gọi; nguy cơ xử lý chậm sự cố ngập nước hoặc kẹt thang máy gây tổn thất tài sản hàng tỷ đồng. |
| **5. Success Metric** | 1. **Thời gian phản hồi:** Rút ngắn thời gian xử lý ban đầu từ 2-4 tiếng xuống **dưới 2 phút/ticket**.<br>2. **Độ chính xác phân loại kép:** Đạt **$\ge 96\%$** cho cả nhánh sự cố kỹ thuật và thủ tục hành chính.<br>3. **Cảnh báo khẩn cấp:** 100% sự cố khẩn cấp (cháy, ngập nước nghiêm trọng, kẹt thang máy) được kích hoạt báo động trong **dưới 30 giây**. |
| **6. Operational Boundary** | **Phạm vi cho phép:** AI được đọc ticket, phân loại tag, gán KTV gợi ý, tra cứu Sổ tay Cư dân và soạn thảo tin nhắn nháp.<br>**Ranh giới cấm nghiêm ngặt (STRICT):**<br>- Mọi tin nhắn gửi cho cư dân **BẮT BUỘC** phải có tag `[DRAFT_ONLY]` để nhân viên CSKH duyệt trước (Bắt buộc Human-in-the-loop).<br>- AI **TUYỆT ĐỐI KHÔNG** được hứa hẹn bồi thường tài chính, không tự ý cam kết đền bù khi chưa có kết luận của Trưởng ban quản lý.<br>- Gặp sự cố đe dọa an toàn tính mạng, AI bắt buộc kích hoạt còi báo động Bước 05 tức thì, không xếp vào hàng chờ thường. |

---

### 3.4. Future-State Flow & Kiến trúc AI Fit

#### A. Phân tích AI-Fit:
* **Rule-based (Regex):** Không thể hiểu được ngôn ngữ tự nhiên đa dạng, tiếng lóng, ngữ cảnh cảm xúc của cư dân.
* **Autonomous Agent (Tự trị hoàn toàn):** Quá rủi ro nếu để Agent tự động cam kết đền bù hoặc tự đóng ticket khi chưa giải quyết xong.
* **Lựa chọn tối ưu:** **LLM Feature Co-pilot (Gemini 2.5 Flash + RAG) kết hợp Human-in-the-loop (HITL).**

#### B. Quy trình tương lai (Future-State Flow):

```text
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ BƯỚC 01         │       │ BƯỚC 02         │       │ BƯỚC 03         │       │ BƯỚC 04         │
│ Cư dân gửi      │       │ 🔵 AI Engine    │       │ 🔵 AI Engine    │       │ 🟢 Human Review │
│ ticket đa kênh  │ ────> │ Phân loại kép & │ ────> │ Tự động gán KTV │ ────> │ CSKH 1-click    │
│ (App/Voice/Pic) │       │ Gán nhãn SLA    │       │ & Tra cứu RAG   │       │ Duyệt [DRAFT]   │
│                 │       │                 │       │                 │       │                 │
│ Hệ thống tự động│       │ Gemini 2.5 Flash│       │ Auto-API & RAG  │       │ Nhân viên (HITL)│
│ Thời gian: Tức thì      │ Thời gian: 2s   │       │ Thời gian: 2s   │       │ Thời gian: 15s  │
└─────────────────┘       └─────────────────┘       └─────────────────┘       └─────────────────┘
                                   │
                                   │ (Nếu phát hiện cháy/kẹt thang/ngập nước)
                                   ▼
                          ┌─────────────────────────────────────────────────────────────┐
                          │ BƯỚC 05: 🔵 BÁO ĐỘNG ĐIỀU PHỐI CỨU HỘ KHẨN CẤP (< 30 GIÂY)  │
                          │ Tự động kích hoạt còi hú, bypass hàng chờ, gọi Hotline BQL  │
                          └─────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
                          ↩️ Kế hoạch Fallback:
                          Nếu AI có độ tự tin < 85% hoặc API gặp sự cố timeout (> 3s),
                          hệ thống tự động chuyển ticket sang chế độ hàng chờ thủ công
                          truyền thống của CSKH, đảm bảo không thất lạc bất kỳ phản ánh nào.
```

---

## 🏁 3. Phase 5 — EVALUATE: Quyết định triển khai

### AI Readiness Checklist:
1. **Dữ liệu & API:** ✅ Có sẵn hàng trăm nghìn ticket lịch sử trên hệ thống Vinhomes Resident để làm tập mẫu (Few-shot) và Sổ tay Cư dân chính thức để xây dựng RAG.
2. **Quản trị rủi ro & Ranh giới:** ✅ Cơ chế ranh giới kép: Tag tiền tố `[DRAFT_ONLY]` bắt buộc duyệt con người (HITL) + Kênh báo động khẩn cấp Bước 05 riêng biệt.
3. **Mức độ sẵn sàng của Stakeholders:** ✅ Ban Quản lý và Khối Dịch vụ Khách hàng Vinhomes đang quyết liệt tìm giải pháp giảm tải ca trực và nâng cao chỉ số hài lòng của cư dân.

### Quyết định của Ban Dự Án Vin Smart Future:
👉 **QUYẾT ĐỊNH: [x] GO (Bắt đầu xây dựng Prototype)**

**Lý giải quyết định (Justification):**
- **Hiệu quả vượt trội:** Cắt giảm thời gian phản hồi ban đầu từ 2-4 tiếng xuống dưới 2 phút (nhanh gấp 9 lần).
- **Chi phí & Rủi ro thấp:** Sử dụng mô hình `gemini-2.5-flash` có tốc độ xử lý siêu nhanh, chi phí API cực rẻ (< 50 VNĐ/ticket), luôn có nhân viên kiểm soát đầu ra trước khi gửi tới cư dân.
