# 02 — Problem Deep-Dive Report: Vin Smart Future

**Dự án:** Hệ thống AI Tiếp nhận, Phân loại & Điều hướng Báo cáo Phản ánh Cư dân (Vinhomes Resident Feedback Dispatcher)  
**Đơn vị áp dụng:** Vinhomes — Phối hợp cùng Vin Smart Future  
**Học viên thực hiện:** Quang Dũng

---

## 🏛️ 1. Bối cảnh & Vai trò

Tôi là **Quang Dũng**, AI Product Engineer tại **Vin Smart Future**. Đơn vị chúng tôi được giao trọng trách nghiên cứu giải pháp AI giúp tối ưu hóa công tác quản lý vận hành đô thị cho **Vinhomes** — nhà phát triển bất động sản đô thị thông minh hàng đầu Việt Nam.

Khảo sát thực tế tại Ban Quản lý các đại đô thị Vinhomes (Vinhomes Ocean Park, Smart City, Grand Park), mỗi ngày hệ thống ứng dụng **Vinhomes Resident** tiếp nhận hàng nghìn phản ánh, khiếu nại từ cư dân. Các nội dung trải dài từ sự cố hạ tầng kỹ thuật (mất nước, rò rỉ đường ống, chập điện, kẹt thang máy), vi phạm trật tự an ninh (đỗ xe sai quy định, làm ồn ban đêm) đến thắc mắc về phí dịch vụ.

Hiện nay, đội ngũ Chăm sóc Cư dân (CSKH) phải đọc thủ công từng ticket, phân loại chuyên mục và gán thủ công cho từng kỹ thuật viên/bảo vệ phụ trách block tòa nhà, sau đó tự viết tay câu trả lời phản hồi cho cư dân. Quy trình thủ công này gây nghẽn nghiêm trọng, làm chậm trễ thời gian xử lý sự cố và ảnh hưởng tiêu cực tới chỉ số hài lòng (CSAT) của cư dân Vinhomes.

---

## 🏗️ 2. Phase 3 — DEEP-DIVE

### 3.1. Current-State Workflow Mapping (Quy trình vận hành hiện tại)

Quy trình xử lý thủ công một phản ánh của cư dân hiện gồm 5 bước tuần tự:

```text
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ Bước 1          │       │ Bước 2          │       │ Bước 3          │       │ Bước 4          │
│ Cư dân gửi      │       │ Đọc hiểu &      │       │ Tra cứu & Gán   │       │ Soạn tin nhắn   │
│ phản ánh trên   │ ────> │ phân loại nhãn  │ ────> │ bộ phận xử lý   │ ────> │ phản hồi xác    │
│ App Vinhomes    │  🔄   │ sự cố           │  🔄   │ theo block nhà  │  🔄   │ nhận cho cư dân │
│                 │       │                 │       │                 │       │                 │
│ Actor: Cư dân   │       │ Actor: CSKH     │       │ Actor: CSKH     │       │ Actor: CSKH     │
│ Thời gian: 2 phút       │ Thời gian: 5 phút 🔴    │ Thời gian: 4 phút       │ Thời gian: 6 phút 🔴
│ In: Text/Ảnh sự cố      │ In: Nội dung ticket     │ In: Danh bạ kỹ thuật    │ In: Thông tin tiếp nhận
│ Out: Ticket thô │       │ Out: Category/Priority  │ Out: Ticket gán KTV     │ Out: Tin nhắn xác nhận
└─────────────────┘       └─────────────────┘       └─────────────────┘       └─────────────────┘
                                                                                       │
                                                                                       ▼
                                                                              ┌─────────────────┐
                                                                              │ Bước 5          │
                                                                              │ Kích hoạt quy   │
                                                                              │ trình khẩn cấp  │
                                                                              │ (nếu cháy/kẹt)  │
                                                                              │                 │
                                                                              │ Actor: CSKH     │
                                                                              │ Thời gian: 1 phút
                                                                              │ Out: Báo động BQL
                                                                              └─────────────────┘

Ký hiệu:
- 🔄 Handoff: Điểm chuyển giao thông tin giữa cư dân -> CSKH -> Danh bạ vận hành -> Kỹ thuật viên hiện trường.
- 🔴 Bottleneck: Bước 2 (Đọc & phân loại nhãn) và Bước 4 (Soạn văn bản phản hồi chuẩn mực cho cư dân), chiếm 11/18 phút.
- ⏱ Tổng thời gian xử lý thủ công ban đầu trung bình: 18 phút/ticket.
- Thời gian chờ thực tế của cư dân: Từ 2 đến 12 tiếng vào giờ cao điểm.
```

---

### 3.2. Problem Statement (6-field) — Chuẩn Vin Smart Future

| Trường thông tin | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Nhân viên Chăm sóc Cư dân (CSKH) và Trưởng ca Quản lý Vận hành Tòa nhà tại các Khu Đô thị Vinhomes. |
| **2. Current Workflow** | Cư dân tạo phản ánh trên App Vinhomes Resident. Nhân viên CSKH mở Dashboard quản trị, đọc nội dung tin nhắn, gán nhãn chuyên mục (Kỹ thuật/An ninh/Cảnh quan/Phí), tra cứu phân công nhân sự theo ca trực và tòa nhà, sau đó tự gõ câu trả lời tiếp nhận gửi lại cho cư dân. Quy trình kéo dài trung bình 18 phút/ticket và lên tới hàng giờ khi lượng phản ánh quá tải. |
| **3. Bottleneck** | **Bước 2 & 4 (chiếm hơn 60% thời gian):** Đọc hiểu mô tả không có cấu trúc của cư dân (đôi khi viết tắt, cảm xúc bức xúc), phân tích mức độ ưu tiên và soạn thảo văn bản phản hồi đúng quy chuẩn văn phong dịch vụ chuẩn Vinhomes 5 sao. |
| **4. Business Impact** | Mỗi đại đô thị tiếp nhận trung bình 1,500 - 2,500 tickets/tuần. Thời gian phản hồi chậm dẫn đến tỷ lệ khiếu nại leo thang (escalation) tăng 25%, cư dân gọi điện dồn dập lên tổng đài gây nghẽn đường dây nóng; nguy cơ xử lý chậm các sự cố hạ tầng kỹ thuật (vỡ ống nước, kẹt thang máy) gây thiệt hại lớn về tài sản. |
| **5. Success Metric** | 1. **Tốc độ phản hồi ban đầu:** Rút ngắn thời gian từ 2-4 tiếng xuống **dưới 3 phút/ticket**.<br>2. **Độ chính xác phân loại:** Tỉ lệ phân loại đúng chuyên mục sự cố và gán đúng tổ kỹ thuật đạt **>= 96%**.<br>3. **An toàn & Cảnh báo khẩn cấp:** 100% phản ánh mang tính nguy cấp (cháy, ngập nước nghiêm trọng, kẹt thang máy, bạo lực) được phát hiện và kích hoạt chuông báo động tới Kỹ thuật trưởng trong vòng **dưới 30 giây**. |
| **6. Operational Boundary** | **Phạm vi cho phép:** AI được đọc nội dung ticket, gắn tag phân loại, trích xuất căn hộ/vị trí, và soạn thảo tin nhắn xác nhận tiếp nhận dạng nháp (Draft).<br>**Ranh giới cấm tuyệt đối (STRICT):**<br>- Mọi tin nhắn phản hồi cư dân do AI soạn thảo **BẮT BUỘC** phải có tag tiền tố `[DRAFT_ONLY]` để nhân viên CSKH duyệt trước khi gửi (Tuyệt đối không để AI tự động gửi tin ra ngoài khi chưa có Human-in-the-loop).<br>- AI **TUYỆT ĐỐI KHÔNG** được hứa hẹn bồi thường tài chính, không cam kết thời gian hoàn thành vượt quá SLA, và không được xếp hàng xử lý thông thường nếu phát hiện dấu hiệu đe dọa tính mạng/an toàn cư dân. |

---

### 3.3. Future-State Flow & AI Fit Matrix

#### A. Phân tích AI-Fit:
* **Rule-based (Regex/Từ khóa):** Dễ bỏ sót khi cư dân dùng tiếng lóng, viết tắt hoặc mô tả gián tiếp (ví dụ: *"hành lang tầng 8 đang có mùi khét lẹt"* -> Rule khó nhận diện chính xác mức độ khẩn cấp).
* **Autonomous Agent (Tác tử tự hành):** Quá rủi ro nếu để Agent tự động chốt phương án bồi thường hoặc tự động đóng ticket cư dân mà không có con người kiểm tra.
* **Lựa chọn tối ưu:** **LLM Feature kết hợp Human-in-the-loop (Co-pilot cho CSKH Vinhomes).** Mô hình LLM (Gemini 2.5 Flash) xử lý hiểu ngôn ngữ tự nhiên, phân loại tag và soạn nháp; nhân viên CSKH chỉ cần 1 click để kiểm tra và phê duyệt.

#### B. Quy trình tương lai (Future-State Flow):

```text
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ Bước 1          │       │ Bước 2          │       │ Bước 3          │       │ Bước 4          │
│ Cư dân gửi      │       │ 🔵 AI Engine    │       │ 🔵 AI Engine    │       │ 🟢 Human Review │
│ phản ánh trên   │ ────> │ Phân loại tag,  │ ────> │ Gán KTV & Draft │ ────> │ CSKH 1-click    │
│ App Vinhomes    │       │ mức ưu tiên     │       │ tin [DRAFT_ONLY]│       │ Duyệt & Gửi tin │
│                 │       │                 │       │                 │       │                 │
│ Hệ thống tự động│       │ Gemini 2.5 Flash│       │ Gemini 2.5 Flash│       │ Nhân viên (HITL)│
│ Thời gian: Tức thì      │ Thời gian: 3s   │       │ Thời gian: 4s   │       │ Thời gian: 20s  │
└─────────────────┘       └─────────────────┘       └─────────────────┘       └─────────────────┘
                                                            │
                                                            ▼ (Nếu sự cố khẩn cấp: Cháy/Kẹt thang)
                                                   ┌─────────────────┐
                                                   │ 🔵 AI Báo động  │
                                                   │ Dispatch Cứu hộ │
                                                   │ Hotline Khẩn cấp│
                                                   └─────────────────┘
                                                            │
                                                            ▼
                                                   ↩️ Fallback Strategy:
                                                   Nếu mô hình AI phản hồi độ tự tin (confidence score)
                                                   thấp (<85%) hoặc lỗi mạng, hệ thống tự động đẩy ticket
                                                   vào hàng chờ thủ công truyền thống của CSKH, đảm bảo
                                                   không một phản ánh nào của cư dân bị thất lạc.
```

---

## 🏁 3. Phase 5 — EVALUATE: Quyết định triển khai

### AI Readiness Checklist:
1. **Dữ liệu & API:** ✅ Hệ thống Vinhomes Resident có sẵn dữ liệu hàng trăm nghìn ticket lịch sử được gán nhãn làm tập mẫu (Few-shot examples).
2. **Quản trị rủi ro & Ranh giới:** ✅ Đảm bảo 100% tin nhắn có tiền tố `[DRAFT_ONLY]` và luôn qua bước duyệt của nhân viên CSKH (HITL), loại bỏ hoàn toàn rủi ro AI phát ngôn sai lệch.
3. **Mức độ sẵn sàng của Stakeholders:** ✅ Ban Quản lý Vinhomes đang tìm kiếm giải pháp chuyển đổi số để giảm tải áp lực nhân sự cho đội ngũ vận hành tại các khu đô thị lớn.

### Quyết định cuối cùng:
👉 **QUYẾT ĐỊNH: [x] GO (Bắt đầu triển khai Prototype)**

**Lý giải quyết định (Justification):**
- **Hiệu quả kinh tế & Vận hành:** Cắt giảm hơn 80% thời gian xử lý thủ công ban đầu của CSKH (từ 18 phút xuống dưới 1 phút), nâng cao chỉ số hài lòng của cư dân.
- **Tính khả thi kỹ thuật:** Năng lực xử lý tiếng Việt của `gemini-2.5-flash` cực kỳ xuất sắc trong việc phân tích sắc thái biểu cảm, trích xuất thực thể (phòng/tầng/sự cố) và draft văn phong trang trọng, lịch thiệp theo chuẩn Vinhomes.
- **Chi phí tối ưu:** Chi phí API cho mỗi ticket chưa tới 50 VNĐ, mang lại ROI (Return on Investment) vượt trội so với chi phí thuê thêm nhân sự trực ca.
