# 01 — Problem Scan & Quick Cards (Cá nhân)

*Lab 02 — AI Product Scoping (Vin Smart Future). Nội dung Phase 1 (SCAN) + Phase 2 (QUICK-ASSESS).*

---

## 🔍 Phase 1 — SCAN: List bài toán (4 Lenses)

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | VinFast | Repetitive (Lặp lại) | So khớp hóa đơn sạc điện tại trạm với dữ liệu phiên sạc (kWh, thời gian, ID xe) để đối soát công nợ — kế toán phải dò tay từng dòng mỗi ngày. |
| 2 | Xanh SM | Stakeholder Pain | Hệ thống gợi ý điểm đón khách không chính xác → tài xế chạy lòng vòng, khách chờ lâu, cả hai đều phàn nàn. |
| 3 | Vinhomes | Time-consuming (Tốn thời gian) | Nhân viên CSKH Ban quản lý mất nhiều thời gian soạn tay phản hồi cho đánh giá 1-sao / phản ánh của cư dân trong app. |
| 4 | Vinpearl / VinWonders | AI-upgrade | Chatbot CSKH đặt vé vui chơi/khách sạn còn rập khuôn, không xử lý được câu hỏi phức tạp và đa ngôn ngữ cho khách quốc tế. |
| 5 | Vinmec | Time-consuming (Tốn thời gian) | Bác sĩ/điều dưỡng mất thời gian đọc và tóm tắt bệnh án, tiền sử dài dòng trước mỗi lượt khám. |
| 6 | WinCommerce | Repetitive (Lặp lại) | Phân loại & định tuyến khiếu nại/feedback khách hàng qua nhiều kênh (app, hotline, MXH) về đúng bộ phận. |

---

## 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

### QUICK PROBLEM CARD #1
- **Bài toán (1 câu):** Tự động trích xuất & so khớp hóa đơn sạc điện với log phiên sạc (kWh, thời gian, ID xe) để đối soát công nợ giữa trạm sạc và đội xe.
- **Công ty thành viên:** ☑ VinFast
- **Ai đang đau (Actor)?** Kế toán vận hành / nhân viên đối soát công nợ trạm sạc.
- **Workflow thủ công hiện tại:**
  1. Thu hóa đơn/biên lai sạc → 2. Mở log phiên sạc từ hệ thống → 3. Dò tay khớp từng dòng (kWh, giờ, ID) → 4. Đánh dấu sai lệch & lập báo cáo đối soát
- **Bước tốn thời gian/lỗi nhất:** Bước 3 — dò khớp tay (⏱ ~3 phút/hóa đơn, 300–500 hóa đơn/ngày)
- **AI hỗ trợ ở bước nào:** Bước 1–3: OCR trích xuất + entity matching tự động, chỉ đẩy case lệch cho người.
- **Metric có số:** Giảm thời gian đối soát mỗi hóa đơn từ 3 min → dưới 20s; tự động khớp đúng ≥90%.
- **Quick Architecture:** ☑ Rule ☑ LLM

### QUICK PROBLEM CARD #2
- **Bài toán (1 câu):** Dự đoán điểm đón khách chính xác hơn dựa trên lịch sử + ngữ cảnh (giờ, khu vực, sự kiện) để giảm thời gian tài xế tìm khách.
- **Công ty thành viên:** ☑ Xanh SM
- **Ai đang đau (Actor)?** Tài xế Xanh SM & khách hàng đặt xe.
- **Workflow thủ công hiện tại:**
  1. Khách đặt xe, ghim điểm đón → 2. Hệ thống gán tài xế → 3. Tài xế tới điểm ghim (thường sai/khó tìm) → 4. Gọi điện xác nhận lại vị trí, chạy lòng vòng
- **Bước tốn thời gian/lỗi nhất:** Bước 3–4 — điểm ghim sai (⏱ ~3–5 phút/chuyến hao phí + hủy chuyến)
- **AI hỗ trợ ở bước nào:** Bước 1–2: gợi ý điểm đón chuẩn từ dữ liệu lịch sử pickup + POI xung quanh.
- **Metric có số:** Giảm thời gian chờ đón trung bình 30% & giảm tỷ lệ hủy chuyến do lệch điểm đón.
- **Quick Architecture:** ☑ Agent

### QUICK PROBLEM CARD #3 ⭐ (Đề tài được chọn để Deep-Dive)
- **Bài toán (1 câu):** Vinhomes Resident Service Copilot — phân loại/định tuyến khiếu nại của cư dân + sinh bản nháp phản hồi & tra cứu thủ tục, giữ Human-in-the-loop cho nội dung nhạy cảm.
- **Công ty thành viên:** ☑ Vinhomes
- **Ai đang đau (Actor)?** Nhân viên CSKH Ban quản lý toà nhà; người thụ hưởng cuối: cư dân.
- **Workflow thủ công hiện tại:**
  1. Nhận yêu cầu/đánh giá của cư dân → 2. Đọc & phân loại vấn đề → 3. Định tuyến sang đúng bộ phận + soạn tay phản hồi/tra cứu thủ tục → 4. Cấp trên duyệt (case nhạy cảm) rồi gửi cư dân
- **Bước tốn thời gian/lỗi nhất:** Bước 3 — soạn tay/tra cứu (⏱ ~10 phút/phản hồi)
- **AI hỗ trợ ở bước nào:** Bước 2–3: phân loại intent + sinh bản nháp phản hồi (LLM + RAG), luôn có người duyệt (HITL).
- **Metric có số:** Giảm thời gian soạn phản hồi từ 10 min → dưới 2 min; ≥80% nháp được duyệt với chỉnh sửa tối thiểu.
- **Quick Architecture:** ☑ LLM
