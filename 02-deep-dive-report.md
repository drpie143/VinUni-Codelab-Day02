# 02 - Deep-Dive Report: Vinhomes Resident Service Copilot

## 1. Executive Summary

Nhóm đề xuất một trợ lý vận hành cho Vinhomes Resident App, tập trung vào hai luồng có cùng điểm nghẽn là xử lý thông tin không có cấu trúc:

1. Phân loại và chuyển khiếu nại cư dân đến đúng bộ phận.
2. Hướng dẫn cư dân chuẩn bị thủ tục thông thường bằng checklist dựa trên tài liệu đã được phê duyệt.

Prototype không thay thế nhân viên ban quản lý. AI chỉ phân tích nội dung, trích xuất dữ liệu, đề xuất tuyến xử lý, hỏi thông tin còn thiếu và tạo bản nháp. Nhân viên vẫn quyết định cuối cùng đối với mọi nội dung liên quan đến phí, pháp lý, tranh chấp, an toàn, quyền lợi cư dân hoặc phê duyệt hồ sơ.

Các con số trong báo cáo là **baseline giả định để thiết kế thử nghiệm**, không phải số liệu đã được Vinhomes xác nhận. Trước khi triển khai cần lấy log 2-4 tuần, đo baseline thực tế và điều chỉnh ngưỡng.

---

# Phase 3 - DEEP-DIVE

## 3.1 Current-State Workflow Mapping

### Phạm vi quy trình

Một yêu cầu điển hình bắt đầu từ tin nhắn trên ứng dụng, cuộc gọi hoặc quầy dịch vụ. Yêu cầu có thể là khiếu nại vận hành, câu hỏi thủ tục, yêu cầu tra cứu trạng thái hoặc tình huống cần ưu tiên.

```text
┌────────────────────┐
│ 1. Cư dân gửi yêu  │
│ cầu qua App/call/  │
│ quầy                │
│ ⏱ 1-2 phút         │
└─────────┬──────────┘
          │ 🔄 Handoff: cư dân -> CSKH
          ▼
┌────────────────────┐
│ 2. Nhân viên đọc   │
│ nội dung, xác định │
│ loại yêu cầu        │
│ ⏱ 3-5 phút 🔴      │
└─────────┬──────────┘
          │ 🔄 Handoff: CSKH -> ban quản lý
          ▼
┌────────────────────┐
│ 3. Hỏi bổ sung tòa,│
│ căn hộ, ảnh, thời  │
│ gian hoặc hồ sơ     │
│ ⏱ 2-5 phút 🔴      │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐       ┌─────────────────────┐
│ 4A. Chuyển khiếu   │──────>│ Ban kỹ thuật/an ninh│
│ nại đúng bộ phận   │       │ /dịch vụ xử lý       │
│ ⏱ 1-3 phút         │       └──────────┬──────────┘
└────────────────────┘                  │
                                        ▼
                              ┌─────────────────────┐
                              │ 5A. Cập nhật trạng  │
                              │ thái và phản hồi    │
                              │ cư dân               │
                              │ ⏱ 2-5 phút          │
                              └─────────────────────┘

          hoặc

┌────────────────────┐
│ 4B. Tra cứu quy    │
│ định/thủ tục và    │
│ điều kiện hồ sơ    │
│ ⏱ 8-12 phút 🔴     │
└─────────┬──────────┘
          │ 🔄 Handoff: CSKH -> cư dân
          ▼
┌────────────────────┐
│ 5B. Soạn và gửi    │
│ hướng dẫn; chờ cư  │
│ dân bổ sung hồ sơ  │
│ ⏱ 3-5 phút         │
└────────────────────┘

🔴 = Bottleneck       🔄 = Handoff
Tổng thời gian ước tính: 9-20 phút/yêu cầu, tùy loại yêu cầu.
```

### Điểm nghẽn chính

1. Nội dung cư dân gửi thường không theo biểu mẫu, có thể chứa nhiều vấn đề trong một tin nhắn.
2. Nhân viên phải xác định ý định, tòa/căn hộ, mức độ ưu tiên và bộ phận tiếp nhận bằng tay.
3. Câu hỏi thủ tục yêu cầu tra cứu nhiều tài liệu; câu trả lời có thể thiếu điều kiện hoặc dùng phiên bản biểu mẫu cũ.
4. Yêu cầu thiếu thông tin bị chuyển qua lại, làm tăng thời gian xử lý và gây khó chịu cho cư dân.

---

## 3.2 Problem Statement - 6 Fields

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Nhân viên CSKH và ban quản lý tòa nhà tiếp nhận yêu cầu; các nhóm kỹ thuật, an ninh, dịch vụ xử lý khiếu nại; cư dân là người gửi và nhận phản hồi. |
| **2. Current Workflow** | Cư dân gửi yêu cầu qua App, điện thoại hoặc quầy. Nhân viên đọc nội dung, xác định loại yêu cầu, hỏi dữ liệu thiếu, tra cứu quy định hoặc chuyển thủ công đến bộ phận liên quan, sau đó cập nhật trạng thái cho cư dân. Quy trình phụ thuộc vào kỹ năng và kinh nghiệm của từng nhân viên. |
| **3. Bottleneck** | Phân loại ý định, trích xuất tòa/căn hộ/loại vấn đề, xác định mức độ ưu tiên, chọn đúng bộ phận và tra cứu điều kiện thủ tục. Các bước này mất khoảng 5-12 phút theo baseline giả định và dễ gây chuyển nhầm. |
| **4. Business Impact** | Cư dân phải chờ lâu hoặc gửi lại yêu cầu; nhân viên lặp lại việc đọc, tra cứu và nhập liệu; bộ phận xử lý nhận phiếu thiếu thông tin; SLA phản hồi có nguy cơ bị trễ. Mức tác động thực tế phải được xác minh bằng log số lượng yêu cầu, thời gian xử lý và tỷ lệ chuyển lại. |
| **5. Success Metric** | Sau pilot: ít nhất 85% yêu cầu được phân loại đúng trong dưới 30 giây; tỷ lệ đề xuất đúng bộ phận đạt ít nhất 90%; giảm thời gian phân loại/chuyển từ baseline 5-8 phút xuống dưới 1 phút; tỷ lệ yêu cầu bị chuyển lại dưới 5%; ít nhất 90% câu hỏi thủ tục phổ biến nhận checklist đúng từ lần đầu; 100% trường hợp nhạy cảm có người duyệt. |
| **6. Operational Boundary** | AI được phân loại, trích xuất, hỏi bổ sung, đề xuất bộ phận và tạo draft/checklist từ kho tài liệu đã duyệt. AI tuyệt đối không tự phê duyệt hồ sơ, tự quyết định phí, kết luận tranh chấp, đưa tư vấn pháp lý/an toàn, tự đóng khiếu nại, tự gửi cam kết cuối cùng hoặc dùng tài liệu ngoài kho được phép. |

---

## 3.3 Future-State Flow & AI Fit

### AI-Fit Matrix

| Lựa chọn | Vai trò | Đánh giá |
|---|---|---|
| **Rule / State Machine** | Danh mục yêu cầu, mức độ khẩn cấp, tuyến bộ phận, quyền hạn và các trường bắt buộc | Bắt buộc dùng cho các quyết định có thể cấu hình và các guardrail an toàn. |
| **LLM Feature** | Hiểu câu chữ tự do, phân loại ý định, trích xuất thông tin, tóm tắt và tạo draft/checklist | Phù hợp cho prototype vì xử lý được nhiều cách diễn đạt của cư dân. |
| **Agentic Loop** | Tự lập kế hoạch và gọi nhiều hệ thống để hoàn thành công việc | Chưa chọn trong pilot vì tăng rủi ro tự ý thay đổi trạng thái hoặc gửi cam kết. |

**Kiến trúc được chọn:** Rule + LLM Feature + Human-in-the-loop.

### Future-State Flow

```text
1. Cư dân gửi yêu cầu
          │
          ▼
2. Rule kiểm tra dữ liệu cơ bản và dấu hiệu khẩn cấp
          │
          ▼
3. 🔵 LLM phân loại ý định và trích xuất trường dữ liệu
   (loại yêu cầu, tòa, căn hộ, mức ưu tiên, dữ liệu thiếu)
          │
          ├── Thiếu dữ liệu bắt buộc -> 🔵 tạo câu hỏi bổ sung
          │                          -> chờ cư dân trả lời
          │
          ├── Khiếu nại thông thường -> Rule đề xuất bộ phận
          │                          -> 🟢 nhân viên review
          │                          -> tạo phiếu chuyển
          │
          └── Thủ tục thông thường -> 🔵 tìm trong kho tài liệu đã duyệt
                                     -> tạo checklist + draft trả lời
                                     -> 🟢 nhân viên review và gửi

Nếu confidence thấp, tài liệu không tìm thấy hoặc nội dung nhạy cảm:
↩️ Fallback -> chuyển nhân viên xử lý thủ công, không tự gửi kết luận.
```

### Human-in-the-loop

Nhân viên phải kiểm tra trước khi gửi hoặc chuyển các trường hợp sau:

- Phí, công nợ, hoàn tiền hoặc thay đổi quyền lợi.
- Tranh chấp giữa cư dân, ban quản lý hoặc bên thứ ba.
- Vấn đề pháp lý, an toàn, khẩn cấp hoặc có nguy cơ gây thiệt hại.
- Hồ sơ thi công, cấp phép, đăng ký có yêu cầu phê duyệt.
- Kết quả có confidence thấp hoặc thông tin cư dân không đầy đủ.

### Fallback

- Nếu model lỗi hoặc timeout: dùng form/rule phân loại thủ công hiện tại.
- Nếu không tìm thấy tài liệu phù hợp: trả lời rằng cần nhân viên kiểm tra, không tự suy đoán.
- Nếu confidence dưới ngưỡng pilot, ví dụ 0.85: chuyển hàng đợi review.
- Nếu phát hiện từ khóa an toàn, pháp lý, tranh chấp hoặc phí: ưu tiên nhân viên.
- Nếu dữ liệu tòa/căn hộ không khớp: hỏi lại hoặc chuyển nhân viên, không tự đoán.

---

# Phase 5 - EVALUATE

## AI Readiness Checklist

| Câu hỏi | Đánh giá hiện tại | Bằng chứng cần bổ sung |
|---|---|---|
| Có dữ liệu mẫu/logs sạch để test chưa? | **NOT YET** | Cần log đã ẩn thông tin cá nhân, bộ nhãn ý định, bộ phận nhận và kết quả xử lý. |
| Rủi ro AI sai có kiểm soát được không? | **Có, trong phạm vi pilot** | Dùng rule, confidence threshold, kho tài liệu được duyệt, HITL và fallback thủ công. |
| Stakeholder sẵn sàng thay đổi quy trình chưa? | **Cần xác nhận** | Cần phỏng vấn CSKH/ban quản lý, thống nhất SLA, quyền duyệt và quy trình xử lý ngoại lệ. |

## Quyết định của nhóm: NOT YET -> chuẩn bị pilot có kiểm soát

Nhóm chưa chọn **GO triển khai rộng** vì chưa có baseline và dữ liệu vận hành đã ẩn danh để đánh giá. Tuy nhiên, nhóm chọn **NOT YET**, không phải NO-GO, vì bài toán có workflow lặp lại, metric đo được và có thể kiểm soát rủi ro bằng Rule + LLM Feature + Human-in-the-loop.

### Điều kiện để chuyển sang GO prototype hẹp

1. Thu thập và ẩn danh một tập test tối thiểu gồm các khiếu nại và câu hỏi thủ tục phổ biến.
2. Chuẩn hóa taxonomy ý định, bộ phận xử lý, mức độ ưu tiên và trường dữ liệu bắt buộc.
3. Tạo kho tài liệu thủ tục có phiên bản, người sở hữu và ngày cập nhật rõ ràng.
4. Chạy offline evaluation, đo accuracy phân loại, tỷ lệ chuyển đúng, độ đầy đủ checklist và tỷ lệ hallucination.
5. Pilot chỉ với 3-5 loại khiếu nại và 2-3 thủ tục phổ biến; mọi output ban đầu là draft để nhân viên duyệt.

### Phạm vi không làm trong pilot

- Không tự động xử lý thanh toán hoặc thu phí.
- Không kết luận tranh chấp hay tư vấn pháp lý.
- Không tự cấp phép, phê duyệt hồ sơ hoặc đóng khiếu nại.
- Không tự gửi thông báo có tính cam kết nếu chưa có nhân viên duyệt.
