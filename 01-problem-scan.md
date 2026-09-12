# Phase 1-2 - Problem Scan & Quick Problem Cards

## Bối cảnh lựa chọn

Nhóm tập trung vào **Vinhomes**, nơi ban quản lý phải tiếp nhận nhiều yêu cầu từ cư dân qua ứng dụng, điện thoại hoặc quầy dịch vụ. Hai hướng nhóm quan tâm là:

1. Phân loại và chuyển khiếu nại cư dân đến đúng bộ phận.
2. Trợ lý hỗ trợ cư dân tra cứu và chuẩn bị thủ tục hành chính.

Nhóm gộp hai hướng này thành một cơ hội sản phẩm chung: **Vinhomes Resident Service Copilot**. Hệ thống tiếp nhận yêu cầu bằng ngôn ngữ tự nhiên, phân loại đúng nhu cầu, chuyển việc đến đúng bộ phận và hỗ trợ cư dân chuẩn bị thủ tục. Các số liệu thời gian trong tài liệu này là **baseline giả định để thiết kế prototype**; cần lấy log thực tế của ban quản lý để xác minh trước khi triển khai.

---

# Phase 1 - SCAN: Tìm kiếm cơ hội

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|---|---|---|
| 1 | **Vinhomes** | Lặp lại + AI-upgrade | Cư dân gửi khiếu nại như mất nước, hỏng đèn, thang máy hoặc tiếng ồn. Nhân viên phải đọc từng yêu cầu, gắn nhãn, xác định tòa/khu vực và chuyển đến đúng ban xử lý. |
| 2 | **Vinhomes** | AI-upgrade + Tốn thời gian | Cư dân hỏi thủ tục đăng ký thi công nội thất, đăng ký thẻ xe, cấp lại thẻ hoặc đăng ký tiện ích. Nhân viên phải tra cứu quy định và trả lời lại nhiều câu hỏi tương tự. |
| 3 | **Vinhomes** | Lặp lại + Stakeholder Pain | Kết hợp phân loại khiếu nại và hướng dẫn thủ tục trong một trợ lý trên Vinhomes Resident: xác định ý định, lấy thông tin còn thiếu, chuyển đúng nơi và tạo checklist hồ sơ cho cư dân. |
| 4 | **VinFast** | Lặp lại | Nhân viên đối chiếu dữ liệu sạc từ nhiều trạm với hóa đơn của đối tác, phát hiện giao dịch thiếu hoặc sai lệch và lập danh sách cần kiểm tra. |
| 5 | **Xanh SM** | Tốn thời gian + Stakeholder Pain | Điều phối viên đọc tin nhắn/cuộc gọi của tài xế về sự cố xe, phân loại mức độ khẩn cấp và chuyển đến điều vận, cứu hộ hoặc kỹ thuật. |
| 6 | **Vinmec** | Tốn thời gian | Bác sĩ hoặc nhân viên y tế soạn bản nháp tóm tắt hồ sơ xuất viện từ nhiều nguồn thông tin, sau đó phải kiểm tra lại trước khi phát hành. |

## Top 3 bài toán được chọn để làm Quick Problem Cards

| Thứ hạng | Bài toán | Lý do chọn |
|---|---|---|
| 1 | **Vinhomes Resident Service Copilot** - kết hợp phân loại/chuyển khiếu nại và trợ lý thủ tục | Gần với nhu cầu nhóm quan tâm, có lượng yêu cầu lặp lại, có thể đo thời gian xử lý và vẫn đặt Human-in-the-loop cho nội dung nhạy cảm. |
| 2 | **Xanh SM phân loại sự cố tài xế và chuyển xử lý** | Có workflow rõ, nhiều handoff và có thể đo SLA xử lý sự cố. |
| 3 | **VinFast đối chiếu giao dịch sạc với hóa đơn đối tác** | Có dữ liệu dạng bảng, tiêu chí đúng/sai rõ và có thể bắt đầu bằng Rule-based trước khi dùng LLM. |

---

# Phase 2 - QUICK-ASSESS: 3 Quick Problem Cards

## QUICK PROBLEM CARD #1 - Vinhomes Resident Service Copilot

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: Cư dân gửi khiếu nại hoặc hỏi thủ tục bằng       │
│ ngôn ngữ tự nhiên. Nhân viên phải phân loại, chuyển đúng   │
│ bộ phận và hướng dẫn cư dân chuẩn bị hồ sơ.                 │
│                                                             │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau? Cư dân; nhân viên CSKH/ban quản lý;            │
│ bộ phận kỹ thuật, an ninh và dịch vụ.                       │
│                                                             │
│ Workflow thủ công hiện tại:                                │
│ 1. Nhận tin nhắn/cuộc gọi/quầy                             │
│ → 2. Đọc và xác định khiếu nại hay thủ tục                 │
│ → 3. Xác định tòa, căn hộ, vấn đề, mức ưu tiên             │
│ → 4. Chuyển bộ phận hoặc tra cứu quy định                  │
│ → 5. Hỏi bổ sung, trả lời và theo dõi                       │
│                                                             │
│ Bottleneck: Phân loại/chuyển (5-8 phút) và tra cứu thủ tục  │
│ (8-12 phút); dễ chuyển nhầm hoặc thiếu điều kiện hồ sơ.    │
│                                                             │
│ AI hỗ trợ: Phân loại ý định, trích xuất dữ liệu, đề xuất   │
│ bộ phận, hỏi thông tin thiếu, tạo checklist và draft.       │
│                                                             │
│ Metric: ≥85% phân loại đúng dưới 30 giây; giảm thời gian    │
│ phân loại xuống dưới 1 phút; chuyển sai <5%; checklist     │
│ đúng từ lần đầu ≥90%.                                      │
│                                                             │
│ Quick Architecture: [x] Rule  [x] LLM  [ ] Agent           │
│                                                             │
│ Boundary: Không tự phê duyệt hồ sơ, phí, tranh chấp,        │
│ pháp lý, an toàn hoặc gửi cam kết cuối cùng. Bắt buộc       │
│ nhân viên review các trường hợp nhạy cảm.                   │
└─────────────────────────────────────────────────────────────┘
```

**Baseline:** Các khoảng thời gian là ước tính dùng để thiết kế prototype, cần xác minh bằng log thực tế của ban quản lý.

---

## QUICK PROBLEM CARD #2 - Xanh SM phân loại sự cố tài xế và chuyển xử lý

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: Phân loại sự cố tài xế và chuyển đúng nhóm       │
│ cứu hộ, kỹ thuật hoặc điều vận.                             │
│ Công ty thành viên: [x] Xanh SM                            │
│                                                             │
│ Ai đang đau? Tài xế; điều phối viên; đội cứu hộ/kỹ thuật.   │
│                                                             │
│ Workflow: 1. Nhận cuộc gọi/tin nhắn → 2. Hỏi thông tin     │
│ thiếu → 3. Phân loại mức độ → 4. Chuyển nhóm xử lý         │
│ → 5. Theo dõi và cập nhật tài xế.                          │
│                                                             │
│ Bottleneck: Đọc nội dung không cấu trúc và chọn tuyến xử lý │
│ (6-10 phút/lượt).                                           │
│                                                             │
│ AI hỗ trợ: Tóm tắt, trích xuất biển số/vị trí/triệu chứng, │
│ phân loại và đề xuất tuyến xử lý.                           │
│                                                             │
│ Metric: 90% phân loại dưới 30 giây; nhập phiếu dưới 2 phút; │
│ recall sự cố khẩn cấp ≥95%.                                 │
│                                                             │
│ Quick Architecture: [x] Rule  [x] LLM  [ ] Agent           │
│ Boundary: Chỉ tạo draft và đề xuất. Điều phối viên phải     │
│ xác nhận trước khi chuyển lệnh cứu hộ.                      │
└─────────────────────────────────────────────────────────────┘
```

---

## QUICK PROBLEM CARD #3 - VinFast đối chiếu giao dịch sạc với hóa đơn đối tác

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán: Đối chiếu giao dịch sạc từ trạm đối tác với       │
│ hóa đơn, tìm dòng thiếu/trùng/lệch và lập danh sách kiểm tra.│
│ Công ty thành viên: [x] VinFast                            │
│                                                             │
│ Ai đang đau? Nhân viên tài chính/vận hành và đối tác.       │
│                                                             │
│ Workflow: 1. Tải dữ liệu → 2. Chuẩn hóa mã/thời gian/trạm  │
│ → 3. So khớp hóa đơn → 4. Kiểm tra ngoại lệ                 │
│ → 5. Gửi người phụ trách xác minh và phê duyệt.             │
│                                                             │
│ Bottleneck: Chuẩn hóa dữ liệu và kiểm tra ngoại lệ          │
│ (30-60 phút/batch theo baseline giả định).                  │
│                                                             │
│ AI hỗ trợ: Phân loại ghi chú/email đối tác và soạn draft    │
│ yêu cầu giải trình; Rule xử lý phần so khớp chính xác.      │
│                                                             │
│ Metric: Tự động so khớp 95%; xử lý batch dưới 10 phút;       │
│ precision ngoại lệ ≥99%.                                    │
│                                                             │
│ Quick Architecture: [x] Rule  [x] LLM  [ ] Agent           │
│ Boundary: Không tự sửa dữ liệu, thay đổi tiền, xác nhận     │
│ hóa đơn hoặc phê duyệt thanh toán.                          │
└─────────────────────────────────────────────────────────────┘
```

---

# Kết luận Phase 2

Nhóm chọn **Quick Problem Card #1 - Vinhomes Resident Service Copilot** làm ứng viên ưu tiên cho Phase 3 Deep-Dive. Đây là một đề tài kết hợp nhưng có thể chia thành các bước rõ ràng:

1. Tiếp nhận yêu cầu.
2. Phân loại khiếu nại hoặc nhu cầu thủ tục.
3. Trích xuất thông tin và phát hiện dữ liệu còn thiếu.
4. Đề xuất bộ phận xử lý hoặc tạo checklist thủ tục.
5. Nhân viên review, gửi phản hồi và theo dõi trạng thái.

Việc kết hợp hai hướng giúp giải quyết cả hai điểm nghẽn trong cùng một trải nghiệm cư dân, nhưng phạm vi prototype ban đầu cần hẹp: **chỉ thử nghiệm một số loại khiếu nại và thủ tục phổ biến**, chưa xử lý tự động các vấn đề pháp lý, phí, tranh chấp, an toàn hoặc phê duyệt hồ sơ.

Card #2 và #3 được giữ lại làm phương án so sánh. Card #2 có giá trị vận hành cao nhưng liên quan đến tình huống khẩn cấp; Card #3 dễ đo lường hơn nhưng phần lớn có thể giải quyết bằng Rule-based, nên ít phù hợp hơn với prototype LLM đầu tiên của nhóm.
