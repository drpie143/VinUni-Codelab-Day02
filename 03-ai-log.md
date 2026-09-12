# 03 — AI Log & Reflection

*Nhật ký phản ánh việc dùng AI (Claude / Gemini) làm thought-partner trong Lab 02.*

> ✍️ Ghi chú: Đây là bản nháp dựa trên quá trình làm thực tế của mình. Hãy đọc lại, chỉnh cho đúng giọng cá nhân và bổ sung ví dụ riêng trước khi nộp.

---

## 1. Mình đã dùng AI vào việc gì?

- **Brainstorm Phase 1 (SCAN):** Dùng 4 Lenses (Repetitive / Time-consuming / AI-upgrade / Stakeholder Pain) để AI gợi ý pain point vận hành ở các công ty thành viên Vingroup, rồi mình chọn lọc lại thành 5–6 bài toán cụ thể.
- **Phase 2 (Quick Cards):** Nhờ AI cấu trúc 3 thẻ bài toán theo đúng template (Actor, workflow, bottleneck có thời gian, điểm AI can thiệp, metric có số).
- **Phase 3 (Deep-Dive):** AI giúp viết Problem Statement 6-field, so sánh Rule vs LLM vs Agent, và vẽ Future-State Flow có đánh dấu AI Step / HITL / Fallback cho đề tài Vinhomes Resident Service Copilot.
- **Phase 4 (Prototype) & Phase 5 (Evaluate):** AI giúp chạy thử `prompt_prototype.py`, đọc autograder và soạn phần đánh giá GO/NOT-YET/NO-GO.

## 2. AI giúp được gì rõ nhất?

- Đẩy nhanh việc **cấu trúc hóa suy nghĩ**: từ ý tưởng rời rạc thành bảng/thẻ có metric đo được.
- **Bắt lỗi mình không để ý:** khi định viết lại prototype sang đề Vinhomes, AI phát hiện autograder chấm `SYSTEM_PROMPT` bằng từ khóa cứng của ví dụ xe điện (`draft_only`, `5%`, `dispatch_mobile_charger`) — nếu đổi chủ đề sẽ **mất điểm Tiêu chí 1**. Nhờ vậy mình quyết định giữ prototype EV để ăn đủ điểm autograder, tách khỏi phần Deep-Dive.

## 3. AI sai / hallucination ở đâu?

- **Bịa số liệu tổn thất:** Khi brainstorm, AI đưa ra các con số "ước tính thống kê" (số hóa đơn/ngày, % hủy chuyến, thời gian xử lý) nghe rất thuyết phục nhưng **không phải số liệu chính thức của Vingroup**. Mình đã đánh dấu đây là con số ước lượng/placeholder, không trích dẫn như sự thật.
- **Tự tin thái quá:** AI ban đầu điền metric rất "đẹp" mà chưa gắn với baseline thực tế, dễ gây ảo tưởng khả thi.

## 4. Mình đã sửa prompt / đặt ranh giới thế nào?

- Yêu cầu AI **ghi rõ đâu là con số ước lượng cần tự kiểm chứng**, thay vì để lẫn vào như dữ liệu thật.
- Ép AI **bám sát template** của worksheet (6-field, quick card) thay vì trả lời tự do.
- Với thiết kế boundary của prototype, mình học được nguyên tắc **Human-in-the-loop**: mọi output AI chỉ là bản nháp `[DRAFT_ONLY]`, và có **Fallback** khi model không tự tin (confidence thấp → chuyển người; thiếu tài liệu nguồn → không bịa). Mình áp chính nguyên tắc này vào Future-State Flow của đề tài Vinhomes.

## 5. Bài học rút ra

- AI là **thought-partner tăng tốc**, không phải nguồn sự thật — luôn phải verify số liệu và ranh giới.
- Ranh giới vận hành (boundary) phải được **viết cứng trong system prompt** và **kiểm thử bằng adversarial input**, vì người dùng/hệ thống thật sẽ cố tình phá.
- Chọn đúng mức AI Fit (Rule vs LLM vs Agent) quan trọng hơn là "dùng AI cho hoành tráng": bài toán Vinhomes chỉ cần **LLM Feature + RAG + HITL**, chưa cần Agentic Loop.
