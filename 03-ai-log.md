# 03 — AI Log & Reflection

> **Người viết:** `dacnghia-lx` · **Công cụ AI đã dùng:** Claude (trợ lý soạn tài liệu, rà soát code, vẽ sơ đồ)
> **Phạm vi:** Quá trình hoàn thiện `01-problem-scan.md`, `02-deep-dive-report.md`, `04-workflow-diagram.png` và `starter-code/prompt_prototype.py` cho bài toán *Vinmec Patient Intake Assistant*.

---

## 1. Tôi đã dùng AI vào việc gì

| Việc | Cách dùng AI | Giá trị nhận được |
|---|---|---|
| Đọc yêu cầu | Yêu cầu AI đọc mục 5 của `README.md`, đối chiếu với `01-worksheet.md`, `02-deliverable-example.md`, `autograder/autograder.py` | AI liệt kê đủ 5 deliverable và phát hiện các tiêu chí ngầm của autograder (tên file, định dạng sơ đồ, SDK, số test case) |
| Brainstorm bài toán (Phase 1) | Quét 4 lenses trên 5 công ty thành viên | 6 bài toán; tôi giữ Vinmec làm trọng tâm vì đã bắt đầu prototype cho Vinmec |
| Viết Quick Cards & tự phản biện | AI đóng vai CFO/Trưởng vận hành khó tính | Mỗi card có 1 đoạn "tự phản biện" chỉ ra chỗ rule-based đã đủ (vd. Vinhomes) |
| Deep-dive & Evaluate | Soạn bảng 6-field, so sánh Rule vs LLM vs Agent, future flow, checklist | Khung báo cáo đầy đủ; tôi chọn hướng "Rule + LLM, rule wins" cho red flag |
| Vẽ sơ đồ | AI viết script matplotlib xuất `04-workflow-diagram.png` | Sơ đồ có bước, handoff, thời gian, bottleneck |
| Rà soát code | AI đọc `prompt_prototype.py` và tìm lỗi logic trong system prompt | Phát hiện mâu thuẫn quy tắc (mục 2.1) |

---

## 2. AI sai / có rủi ro ở đâu, và tôi đã sửa thế nào

### 2.1. Mâu thuẫn ngay trong system prompt (lỗi thật, đã sửa)
Phiên bản system prompt đầu tiên có hai quy tắc đá nhau:
- *Rule 1:* "Every response **MUST begin** with `[NOT_A_DIAGNOSIS]`".
- *Rule 2:* khi có red flag, "You must **FIRST** output this JSON".

→ Ở ca cấp cứu, mô hình buộc phải vi phạm một trong hai. Kiểu mâu thuẫn này khiến LLM chọn ngẫu nhiên giữa hai quy tắc, nên kết quả test không ổn định. **Cách sửa:** viết lại ranh giới cho rõ: thẻ áp dụng cho *phần tin nhắn gửi bệnh nhân*; dòng JSON escalate là dữ liệu cho hệ thống và là thứ **duy nhất** được đứng trước thẻ. Test case 1 giờ kiểm tra cả hai: có JSON escalate **và** phần tin nhắn vẫn mở đầu bằng thẻ.

### 2.2. Số liệu "nghe rất thật" nhưng là giả định (rủi ro hallucination)
AI đưa ra các con số như *400 yêu cầu/ngày, 12% đến sai khoa, 15% bỏ cuộc, 45% ca cần điều dưỡng*. Không có con số nào được trích từ dữ liệu thật của Vinmec. Nếu để nguyên, báo cáo sẽ trông như có bằng chứng trong khi thực ra không có.
**Cách sửa:** đánh dấu mọi con số là *(giả định)* ở đầu mỗi file, ghi rõ bước đầu khi triển khai là đo baseline từ log tổng đài/CRM. Ước tính chi phí API cũng chỉ ghi ở mức bậc độ lớn và yêu cầu kiểm tra lại bảng giá, không ghi một con số chính xác.

### 2.3. Không "bịa" kết quả chạy prototype
Môi trường soạn thảo không có API key nên chưa chạy được mô hình thật. Bảng kết quả trong `02-deep-dive-report.md` được để ⏳ *"Điền sau khi chạy"* thay vì ghi ✅ cho đẹp. Các hàm kiểm tra (đọc JSON, kiểm tra thẻ, regex liều thuốc) đã được thử với output giả lập, ví dụ để chắc rằng chuỗi "18kg" trong đề bài không bị nhận nhầm là liều thuốc.

### 2.4. Lệch giữa code của tôi và autograder
- Autograder chấm tiêu chí 2 bằng cách tìm chữ `genai`/`generativeai` trong `evaluate_prompt()`. Bản code cũ chỉ gọi OpenAI → mất điểm dù chạy đúng. **Sửa:** dùng Gemini 2.5 Flash (`google-genai`) làm mặc định đúng như hướng dẫn khóa học, giữ OpenAI làm phương án dự phòng khi chỉ có `OPENAI_API_KEY`.
- Worksheet yêu cầu **≥ 3** adversarial tests, còn template code chỉ yêu cầu ≥ 2. **Sửa:** thêm Test 3 (prompt injection "developer mode" hỏi liều paracetamol cho trẻ).
- Autograder tiêu chí 1 tìm từ khóa của ví dụ Xanh SM (`draft_only`, `5%`, `dispatch_mobile_charger`) trong `SYSTEM_PROMPT`. Use case Vinmec không có các khái niệm này. **Quyết định:** không nhồi từ khóa không liên quan vào prompt y tế chỉ để lấy điểm, vì làm vậy vừa sai mục đích bài lab vừa có thể làm mô hình bối rối. Tôi sẽ hỏi giảng viên xem tiêu chí này có áp dụng cho use case khác ví dụ không.

### 2.5. Sơ đồ lần đầu bị lỗi hiển thị
Bản render đầu tiên bị đè chữ (nhãn BOTTLENECK đè tiêu đề, nhãn HANDOFF đè lên ô) và font không có ký tự ⏱. **Sửa:** nới khoảng cách giữa các ô, đưa nhãn bottleneck xuống dòng riêng, thay ký tự thiếu font, rồi xem lại ảnh trước khi nộp. Bài học: phải tự mở và nhìn output, không tin là AI "chắc đã đúng".

---

## 3. Ranh giới tôi đặt ra khi làm việc với AI

1. **AI đề xuất, tôi quyết định:** chọn bài toán, chọn mức AI Fit, quyết định GO/NOT YET là của tôi/nhóm; AI chỉ đưa ra lập luận và phản biện.
2. **Không có số liệu thì ghi "giả định":** mọi con số AI đưa ra phải được gắn nhãn nếu chưa có nguồn.
3. **Không ghi kết quả chưa chạy.**
4. **Với y tế, chọn phía an toàn:** khi AI gợi ý "LLM tự quyết red flag là đủ", tôi giữ thêm lớp rule quyết định và nguyên tắc "rule wins".
5. **Kiểm tra lại bằng công cụ:** chạy thử hàm, mở ảnh, đối chiếu với autograder, thay vì đọc lướt.

---

## 4. Bài học rút ra

- AI rất nhanh ở việc **dựng khung và brainstorm**, nhưng chính những phần "trông chuyên nghiệp" (số liệu, kết quả test) lại là chỗ dễ sai nhất. Giá trị của tôi nằm ở việc kiểm chứng.
- Viết system prompt giống viết đặc tả: hai quy tắc mâu thuẫn thì mô hình không thể tuân thủ cả hai. Cần đọc prompt như một người review code.
- Adversarial test phải **tấn công đúng ranh giới mình sợ nhất** (nói giảm triệu chứng, giả danh bác sĩ, prompt injection hỏi liều thuốc), không chỉ là test cho có.
- Lần sau tôi sẽ đưa AI ngay từ đầu: (1) file chấm điểm, (2) yêu cầu "đánh dấu mọi số liệu chưa có nguồn", (3) yêu cầu tự kiểm tra mâu thuẫn trong prompt trước khi viết test.

---
