# 01 — Problem Scan & Quick Problem Cards (Vin Smart Future)

**Học viên:** Quang Dũng  
**Đơn vị:** Vin Smart Future (Vingroup)  
**Mảng trọng tâm:** Di chuyển xanh & Vận hành thông minh (Xanh SM, VinFast, Vinhomes)

---

## 🔍 Phase 1 — SCAN: Bảng quét cơ hội (4 Lenses)

Quét qua các hoạt động nghiệp vụ thực tế của các công ty thành viên Vingroup dựa trên **4 Lenses**:
1. **Lặp lại (Repetitive):** Tác vụ lặp đi lặp lại nhiều lần hằng ngày.
2. **Tốn thời gian (Time-consuming):** Tác vụ ngốn thời gian xử lý thủ công của nhân viên/chuyên viên.
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ khách hàng, phân loại hoặc xử lý ngôn ngữ tự nhiên còn rập khuôn, chậm trễ.
4. **Pain từ người khác (Stakeholder Pain):** Bottleneck gây tắc nghẽn, phàn nàn từ khách hàng hoặc nhân viên thực địa.

### Danh sách các bài toán phát hiện:

| # | Công ty thành viên | Lens áp dụng | Mô tả bài toán & Bottleneck thực tế |
|---|--------------------|--------------|--------------------------------------|
| 1 | **Xanh SM (GSM)** | Tốn thời gian | Điều phối viên xử lý thủ công các báo cáo khẩn cấp từ tài xế về sự cố cạn kiệt pin thực địa hoặc lỗi trụ sạc (mất 12-15 phút/cuộc gọi để tra cứu tọa độ, tìm trạm trống và draft tin nhắn chỉ đường). |
| 2 | **VinFast** | Lặp lại | Đối chiếu và so khớp dữ liệu hóa đơn sạc điện từ hàng nghìn trụ sạc đối tác công cộng với chỉ số điện năng tiêu thụ thực tế của từng xe điện hàng tuần. |
| 3 | **Vinhomes** | AI-upgrade | Phân loại tự động và route các phản ánh, khiếu nại của cư dân (sự cố điện nước, an ninh, vệ sinh tòa nhà) gửi qua ứng dụng Vinhomes Resident tới đúng tổ kỹ thuật và draft phản hồi theo quy chuẩn. |
| 4 | **Xanh SM (GSM)** | Pain từ người khác | Tự động phân tích lý do khách hàng hủy chuyến từ file ghi âm tổng đài và ghi chú nhanh của tài xế để nhận diện các điểm đón thường xuyên bị trễ do nghẽn đường. |
| 5 | **Vinmec** | Tốn thời gian | Trích xuất và soạn thảo tóm tắt hồ sơ bệnh án xuất viện (Discharge Summary) từ kết quả xét nghiệm và ghi chú lâm sàng của bác sĩ, giảm tải thời gian hành chính cho đội ngũ y tế. |
| 6 | **Vinpearl** | AI-upgrade | Hỗ trợ phân tích đánh giá đa kênh (Google Maps, Agoda, Booking.com) và tự động tạo phiếu hỗ trợ khẩn cấp nếu phát hiện phản hồi tiêu cực về vệ sinh phòng nghỉ hoặc thái độ phục vụ. |

---

## 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Chọn lọc **3 bài toán tiềm năng nhất** từ danh sách trên:

---

### 🎴 Thẻ bài toán #1: Xanh SM — Xử lý sự cố sạc pin và điều phối cứu hộ thực địa

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                   │
│                                                                         │
│ Bài toán (1 câu): Hỗ trợ điều phối viên Xanh SM tra cứu trạm sạc trống,  │
│ soạn thảo chỉ dẫn đường đi và kích hoạt xe sạc cứu hộ di động khẩn cấp. │
│ Công ty thành viên: [x] Xanh SM (GSM)   [ ] VinFast   [ ] Vinhomes      │
│                                                                         │
│ Ai đang đau (Actor)?                                                    │
│ - Tài xế Xanh SM: Đang mắc kẹt trên đường, lo lắng hết pin dừng xe.     │
│ - Điều phối viên (Dispatcher): Quá tải thao tác trên nhiều dashboard.   │
│                                                                         │
│ Workflow thủ công hiện tại (5 bước):                                    │
│   1. Nhận cuộc gọi khẩn ──> 2. Tra cứu tọa độ GPS xe trên hệ thống     │
│   ──> 3. Lọc trạm sạc VinFast còn trụ trống ──> 4. Soạn SMS chỉ đường  │
│   ──> 5. Gọi đội xe cứu hộ sạc di động (nếu pin < 5%)                   │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & 4 (⏱ 10 phút/lượt)           │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 & 4 (Đồng bộ vị trí GPS,    │
│ tự động lọc trạm phù hợp theo loại xe và draft tin nhắn chỉ dẫn chuẩn). │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│ - Rút ngắn thời gian xử lý sự cố từ 15 phút ──> dưới 3 phút/lượt.      │
│ - Tỉ lệ chỉ dẫn đúng loại trụ sạc còn trống đạt trên 98%.              │
│                                                                         │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM Feature  [ ] Agent     │
└─────────────────────────────────────────────────────────────────────────┘
```

---

### 🎴 Thẻ bài toán #2: Vinhomes — Phân loại & Phản hồi khiếu nại cư dân

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                   │
│                                                                         │
│ Bài toán (1 câu): Tự động phân loại mức độ khẩn cấp phản ánh của cư dân  │
│ Vinhomes Resident và điều phối tới đúng đội kỹ thuật tòa nhà.           │
│ Công ty thành viên: [ ] VinFast   [ ] Xanh SM   [x] Vinhomes            │
│                                                                         │
│ Ai đang đau (Actor)?                                                    │
│ - Cư dân: Bức xúc khi sự cố rò rỉ nước, hỏng thang máy phản hồi chậm.   │
│ - Nhân viên CSKH/Ban quản lý tòa nhà: Đọc thủ công hàng trăm tin/ngày.  │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Nhận ticket trên App ──> 2. Nhân viên đọc nội dung và phân loại    │
│   ──> 3. Chuyển ticket cho Trưởng nhóm kỹ thuật từng block tòa nhà      │
│   ──> 4. Soạn email/thông báo xác nhận tiếp nhận cho cư dân            │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 4 (⏱ 15 phút/ticket)          │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 4 (Phân loại tag sự cố   │
│ và draft sẵn tin nhắn xác nhận chuẩn văn phong Vinhomes).                │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│ - Giảm thời gian phản hồi ban đầu từ 2 giờ ──> dưới 5 phút.             │
│ - Tỉ lệ phân loại chính xác bộ phận phụ trách đạt >= 95%.                │
│                                                                         │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM Feature  [ ] Agent     │
└─────────────────────────────────────────────────────────────────────────┘
```

---

### 🎴 Thẻ bài toán #3: VinFast — Chẩn đoán sơ bộ mô tả lỗi xe điện từ khách hàng

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                                   │
│                                                                         │
│ Bài toán (1 câu): Trợ lý tiếp nhận ngôn ngữ tự nhiên, phân tích mô tả   │
│ tiếng ồn/hiện tượng lạ của xe điện VF5/VF8 để đề xuất mã lỗi sơ bộ.    │
│ Công ty thành viên: [x] VinFast   [ ] Xanh SM   [ ] Vinhomes            │
│                                                                         │
│ Ai đang đau (Actor)?                                                    │
│ - Chủ xe VinFast: Khó diễn tả chính xác thuật ngữ kỹ thuật ô tô.        │
│ - Kỹ thuật viên tiếp nhận dịch vụ (Service Advisor): Mất nhiều thời     │
│   gian hỏi lặp đi lặp lại triệu chứng của xe.                           │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Khách hàng mang xe đến Xưởng dịch vụ và mô tả bằng lời nói         │
│   ──> 2. KTV tra cứu sổ tay kỹ thuật và danh sách mã lỗi DTC            │
│   ──> 3. Cắm máy chẩn đoán quét OBD-II                                  │
│   ──> 4. Lập phiếu báo giá và kế hoạch sửa chữa                         │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 1 & 2 (⏱ 20 phút trao đổi)        │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 1 (Trợ lý AI trên app VinFast│
│ thu thập trước mô tả triệu chứng và gợi ý bộ phận nghi vấn).            │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│ - Giảm thời gian tiếp nhận xe tại xưởng từ 30 phút xuống dưới 12 phút.  │
│ - Khách hàng đặt trước lịch kiểm tra đúng nhóm kỹ thuật viên đạt 90%.   │
│                                                                         │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM Feature  [ ] Agent     │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 🎯 Kết luận lựa chọn bài toán cho Deep-Dive:
Nhóm thống nhất chọn **Thẻ bài toán #1: Xanh SM — Xử lý sự cố sạc pin và điều phối cứu hộ thực địa** để thực hiện phân tích sâu (Deep-Dive).

**Lý do chọn:**
1. Tác động kinh doanh tức thì: Mỗi xe taxi điện ngừng hoạt động gây rò rỉ trực tiếp doanh thu cuốc xe của Xanh SM.
2. Ranh giới an toàn (Operational Boundary) rõ ràng: Có điều kiện pin < 5% mang tính an toàn cao, rất phù hợp để xây dựng và kiểm thử bản mẫu prompt prototype (HITL và Fallback).
