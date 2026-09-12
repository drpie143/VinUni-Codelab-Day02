# 02 — Problem Deep-Dive Report: Vin Smart Future

**Dự án:** Hệ thống Trợ lý Điều phối Sự cố Pin & Cứu hộ Xe điện Thông minh  
**Đơn vị áp dụng:** GSM (Xanh SM) — Phối hợp cùng Vin Smart Future  
**Học viên thực hiện:** Quang Dũng

---

## 🏛️ 1. Bối cảnh & Vai trò

Tại **Vin Smart Future**, chúng tôi nhận nhiệm vụ hiện đại hóa quy trình vận hành đội xe taxi điện cho **Xanh SM (GSM)**. Qua điều tra thực địa tại Trung tâm Điều vận Xanh SM Hà Nội, vấn đề nhức nhối nhất của các điều phối viên (Dispatchers) vào các khung giờ cao điểm là xử lý các cuộc gọi khẩn cấp khi xe điện của tài xế sắp cạn pin, bị kẹt trạm sạc hoặc gặp sự cố nguồn điện thực địa.

Quy trình thủ công hiện tại yêu cầu điều phối viên phải thao tác qua 3 màn hình nghiệp vụ độc lập: Dashboard GPS xe, Bản đồ trạng thái trạm sạc VinFast, và Phần mềm nhắn tin nội bộ. Việc này gây lãng phí 12-15 phút cho mỗi ca sự cố, dẫn đến nguy cơ xe hết pin giữa đường gây tắc nghẽn và hủy cuốc.

---

## 🏗️ 2. Phase 3 — DEEP-DIVE

### 3.1. Current-State Workflow Mapping (Quy trình vận hành hiện tại)

Quy trình xử lý thủ công gồm 5 bước tuần tự:

```text
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ Bước 1          │       │ Bước 2          │       │ Bước 3          │       │ Bước 4          │
│ Nhận cuộc gọi   │       │ Tra cứu tọa độ  │       │ Tìm kiếm trạm   │       │ Soạn tin nhắn   │
│ báo hết pin     │ ────> │ định vị xe      │ ────> │ sạc còn trụ     │ ────> │ chỉ dẫn & gửi   │
│                 │  🔄   │                 │  🔄   │                 │  🔄   │ cho tài xế      │
│ Operator: Tổng đài      │ Operator: Điều vận      │ Operator: Điều vận      │ Operator: Điều vận
│ Thời gian: 2 phút       │ Thời gian: 2 phút       │ Thời gian: 5 phút 🔴    │ Thời gian: 5 phút 🔴
│ In: Cuộc gọi thoại      │ In: Biển số xe/Mã xe    │ In: Tọa độ GPS, loại xe │ In: Địa chỉ trạm sạc
│ Out: Ticket sự cố       │ Out: Kinh độ, vĩ độ     │ Out: Danh sách trạm     │ Out: SMS / App Driver
└─────────────────┘       └─────────────────┘       └─────────────────┘       └─────────────────┘
                                                                                       │
                                                                                       ▼
                                                                              ┌─────────────────┐
                                                                              │ Bước 5          │
                                                                              │ Điều xe sạc pin │
                                                                              │ cứu hộ di động  │
                                                                              │ (nếu pin < 5%)  │
                                                                              │ Operator: Điều vận
                                                                              │ Thời gian: 1 phút
                                                                              │ Out: Lệnh cứu hộ│
                                                                              └─────────────────┘

Ký hiệu:
- 🔄 Handoff: Điểm chuyển đổi thao tác/hệ thống giữa các màn hình nghiệp vụ.
- 🔴 Bottleneck: Điểm nghẽn gây tốn nhiều thời gian và dễ nhầm lẫn thông tin nhất (Bước 3 & Bước 4).
- ⏱ Tổng thời gian xử lý sự cố thủ công trung bình: 15 phút/lượt.
```

---

### 3.2. Problem Statement (6-field) — Tiêu chuẩn Vin Smart Future

| Trường thông tin | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Điều phối viên trực ca (Dispatcher) tại Trung tâm Điều vận Xanh SM toàn quốc. |
| **2. Current Workflow** | Khi nhận cuộc gọi báo pin khẩn từ tài xế, điều phối viên nhập biển số để tìm xe trên bản đồ định vị nội bộ, mở Dashboard trạm sạc VinFast để tìm trụ sạc tương thích còn trống gần nhất, viết tin nhắn SMS/In-app hướng dẫn đường đi, hoặc gọi đội cứu hộ di động nếu pin quá thấp. Quy trình tốn trung bình 15 phút/lượt. |
| **3. Bottleneck** | **Bước 3 & 4 (chiếm 10/15 phút):** Tra cứu thủ công loại cổng sạc phù hợp với dòng xe (VF5, VFe34, VF8) trên bản đồ và soạn thảo văn bản hướng dẫn thân thiện, chuẩn xác bằng tiếng Việt. |
| **4. Business Impact** | Toàn hệ thống tiếp nhận trung bình ~85 sự cố pin/ngày tại các thành phố lớn. Tiêu tốn ~21.25 giờ làm việc/ngày của đội ngũ điều vận. Tăng thời gian chết (idle time) của xe, rò rỉ doanh thu ước tính hơn 1.2 tỷ VNĐ/năm do mất cuốc và gây ức chế tâm lý cho tài xế đối tác. |
| **5. Success Metric** | 1. **Hiệu suất thời gian:** Giảm thời gian xử lý sự cố từ 15 phút xuống dưới **3 phút/lượt**.<br>2. **Độ chính xác kỹ thuật:** Đảm bảo 100% trạm sạc đề xuất tương thích với cổng sạc của dòng xe.<br>3. **An toàn vận hành:** 100% các trường hợp pin < 5% được cảnh báo và kích hoạt xe sạc cứu hộ di động, không để xe chết máy giữa đường. |
| **6. Operational Boundary** | **Phạm vi cho phép:** AI được quyền đọc API tọa độ GPS xe, API trạng thái trạm sạc VinFast, và tự động soạn thảo tin nhắn hướng dẫn.<br>**Ranh giới cấm tuyệt đối (STRICT):**<br>- Mọi tin nhắn đề xuất của AI bắt buộc phải gắn tiền tố `[DRAFT_ONLY]` để điều phối viên con người kiểm duyệt trước khi bấm gửi (Bắt buộc Human-in-the-loop).<br>- Nếu dung lượng pin dưới 5%, AI **TUYỆT ĐỐI KHÔNG** được chỉ đường tới trạm sạc xa hơn 5km mà phải ngay lập tức đề xuất lệnh điều xe cứu hộ sạc di động: `{"action": "dispatch_mobile_charger", "reason": "..."}`. |

---

### 3.3. Future-State Flow & AI Fit Matrix

#### A. Phân tích AI-Fit:
* **Rule-based:** Chỉ xử lý được việc lọc bán kính cố định, nhưng không linh hoạt khi tổng hợp thông tin, không tự động viết được tin nhắn chỉ dẫn thân thiện, cá nhân hóa cho tài xế theo ngữ cảnh thời gian thực.
* **Agentic Loop (Đa tác tử tự trị hoàn toàn):** Tiềm ẩn rủi ro quá lớn nếu AI tự động ra lệnh điều xe cứu hộ hoặc tự gửi tin sai khiến xe điện cạn pin giữa ngã tư giờ cao điểm.
* **Lựa chọn tối ưu:** **LLM Feature kết hợp Rule Validation (Human-in-the-loop Co-pilot).** AI đóng vai trò Co-pilot chuẩn bị sẵn dữ liệu và bản nháp; điều phối viên con người giữ quyền quyết định cuối cùng.

#### B. Quy trình tương lai (Future-State Flow):

```text
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ Bước 1          │       │ Bước 2          │       │ Bước 3          │       │ Bước 4          │
│ Nhận cuộc gọi/  │       │ 🔵 AI Engine    │       │ 🔵 AI Engine    │       │ 🟢 Human Review │
│ Cảnh báo pin ảo │ ────> │ Auto-fetch GPS  │ ────> │ Tạo Draft &     │ ────> │ Dispatcher      │
│                 │       │ & Trạm sạc gần  │       │ Gắn [DRAFT_ONLY]│       │ Duyệt & Bấm gửi │
│ Hệ thống tự động        │ Hệ thống tự động        │ Gemini 2.5 Flash        │ Con người (HITL)│
│ Thời gian: 10s          │ Thời gian: 5s           │ Thời gian: 5s           │ Thời gian: 30s  │
└─────────────────┘       └─────────────────┘       └─────────────────┘       └─────────────────┘
                                                            │
                                                            ▼ (Nếu pin < 5%)
                                                   ┌─────────────────┐
                                                   │ 🔵 AI Kích hoạt │
                                                   │ Dispatch Mobile │
                                                   │ Charger Lệnh    │
                                                   └─────────────────┘
                                                            │
                                                            ▼
                                                   ↩️ Fallback Strategy:
                                                   Nếu API Gemini gặp sự cố mạng hoặc timeout (>5s),
                                                   hệ thống tự động chuyển sang hiển thị bảng danh sách
                                                   trạm sạc theo khoảng cách thuần (Rule-based) để điều
                                                   phối viên thao tác thủ công, không làm gián đoạn ca trực.
```

---

## 🏁 3. Phase 5 — EVALUATE: Quyết định triển khai

### AI Readiness Checklist:
1. **Dữ liệu & API:** ✅ Có sẵn API định vị xe Xanh SM và API trạng thái trạm sạc VinFast theo thời gian thực.
2. **Quản trị rủi ro & Ranh giới:** ✅ Đã thiết lập ranh giới an toàn kép qua thẻ tiền tố `[DRAFT_ONLY]` và ngưỡng pin tối thiểu 5%, kết hợp cơ chế Human-in-the-loop.
3. **Mức độ sẵn sàng của Stakeholders:** ✅ Đội ngũ điều vận Xanh SM rất mong muốn có công cụ tự động hóa giảm tải áp lực trực tổng đài.

### Quyết định của Ban Dự Án Vin Smart Future:
👉 **QUYẾT ĐỊNH: [x] GO (Triển khai xây dựng Prototype)**

**Lý giải quyết định (Justification):**
- **Hiệu quả rõ rệt:** Giảm thời gian xử lý sự cố từ 15 phút xuống dưới 3 phút (tiết kiệm hơn 80% thời gian xử lý).
- **Chi phí & Độ phức tạp thấp:** Mô hình `gemini-2.5-flash` có tốc độ phản hồi mili-giây, chi phí API cực thấp và dễ dàng tích hợp vào hệ sinh thái ứng dụng của Vin Smart Future.
- **Tính an toàn tuyệt đối:** Có quy chế phê duyệt Human-in-the-loop và phương án Fallback dự phòng, bảo vệ thương hiệu và cam kết chất lượng dịch vụ của Xanh SM.
