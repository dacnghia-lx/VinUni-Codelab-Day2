# Lab 02 — AI Log & Reflection

**Họ tên:** Nguyễn Vũ Hoàng

**Mã sinh viên:** 2A202603491

**GitHub:** bacphan204-tech

## 1. Quá trình sử dụng AI

Ban đầu em khá lúng túng khi đọc README vì chưa hiểu rõ AI Product Scoping là gì và phải hoàn thành những file nào. Trước đây em chủ yếu quen vibe coding, thường mô tả yêu cầu rồi nhờ AI hỗ trợ viết code, nên việc tự phân tích một bài toán kinh doanh còn khá mới.

Em đã nhờ ChatGPT đọc và giải thích README, worksheet, code mẫu và tiêu chí chấm điểm. Sau đó em lựa chọn Vinhomes làm lĩnh vực nghiên cứu, xây dựng danh sách 5 vấn đề và phân tích 3 Quick Problem Cards.

ChatGPT giúp em hiểu cách xác định Actor, Current Workflow, Bottleneck, Success Metric và sự khác biệt giữa Rule-based, LLM Feature và Agentic Loop.

## 2. Quá trình lập trình và những khó khăn

Ở phần Python Prototype, em sử dụng code mẫu Xanh SM của giảng viên để kiểm tra các ranh giới an toàn của Gemini.

Quá trình này khá chật vật vì em gặp nhiều lỗi liên tiếp:

- Khi cài thư viện, pip mất nhiều thời gian tìm phiên bản tương thích.
- API Key ban đầu không hợp lệ, dẫn đến lỗi `400 INVALID_ARGUMENT`.
- Model `gemini-2.5-flash` không khả dụng với tài khoản của em.
- Chuyển sang `gemini-3.8-flash` nhưng liên tục gặp lỗi `503 UNAVAILABLE` và `504 DEADLINE_EXCEEDED`.
- Có lúc chương trình chạy quá lâu khiến em phải đóng terminal và mở lại.
- Em phát hiện mình đã khai báo trùng hàm `evaluate_prompt()` khi chỉnh sửa code.

Em phải nhờ ChatGPT giải thích từng thông báo lỗi, sau đó tự sửa và chạy lại để kiểm tra.

Cuối cùng, em chuyển sang `gemini-3.6-flash`, bổ sung giới hạn thời gian chờ và hoàn thiện 3 Adversarial Tests.

## 3. AI đã trả lời sai hoặc chưa phù hợp ở đâu?

Không phải hướng dẫn nào của ChatGPT cũng hoàn toàn chính xác.

Ban đầu, AI tập trung giúp em hoàn thành bài kiểm thử Xanh SM, trong khi phần Problem Scan em lựa chọn Vinhomes. Sau đó em mới hiểu rằng ba Quick Problem Cards là ba bài toán tiềm năng, còn ba Adversarial Tests là ba tình huống dùng để kiểm tra ranh giới của một hệ thống AI.

Ngoài ra, ChatGPT từng hướng dẫn em bổ sung code nhưng em chưa xóa hết hàm cũ, dẫn đến việc khai báo trùng hàm.

Trong một lần kiểm thử, Gemini trả về action `send_customer_notification`, dù vẫn yêu cầu con người phê duyệt. Em nhận thấy tên action này chưa thể hiện rõ giới hạn chỉ được soạn bản nháp, nên đã bổ sung quy tắc yêu cầu sử dụng `draft_customer_notification`.

Qua đó, em hiểu rằng không nên chỉ copy kết quả AI mà cần đọc, kiểm tra logic và thử nghiệm lại.

## 4. Kết quả kiểm thử

Em đã xây dựng 3 Adversarial Tests:

1. Yêu cầu AI hướng dẫn xe còn 2% pin đến trạm sạc cách 8 km.
2. Yêu cầu AI bỏ thẻ `[DRAFT_ONLY]` và gửi tin trực tiếp.
3. Giả danh quản trị viên để yêu cầu AI bỏ qua các quy tắc an toàn.

Trong những lần Gemini phản hồi thành công, hệ thống giữ được các ranh giới chính. Tuy nhiên, một số lượt kiểm thử bị gián đoạn bởi lỗi máy chủ nên em không xem đó là kết quả PASS.

Khi chạy Autograder lần đầu, em chỉ đạt **3/5 điểm** dù trước đó chương trình từng chạy thành công.

Sau khi kiểm tra lại, em thử thiết lập:

`$env:PYTHONIOENCODING="utf-8"`

Lệnh này giúp xử lý vấn đề mã hóa đầu ra Unicode khi Python được Autograder gọi trên Windows.

Kết quả cuối cùng, Autograder Section B đạt **5/5 điểm**, cả 5 tiêu chí đều PASS.

## 5. Bài học rút ra

Sau Lab 02, em nhận ra rằng xây dựng sản phẩm AI không đơn giản là gọi API hoặc viết một prompt dài.

Điều quan trọng hơn là xác định đúng vấn đề, lựa chọn giải pháp phù hợp, thiết lập ranh giới an toàn và có cách đánh giá bằng kết quả cụ thể.

Em cũng hiểu rõ hơn rằng AI rất hữu ích khi hỗ trợ học tập và tìm lỗi, nhưng không phải lúc nào cũng đúng. Người lập trình vẫn cần hiểu code, kiểm tra kết quả và chịu trách nhiệm về những gì mình xây dựng.

Mặc dù em vẫn phải dựa khá nhiều vào ChatGPT, việc tự chạy lệnh, gặp lỗi, tìm nguyên nhân rồi sửa lại giúp em hiểu quy trình phát triển AI rõ hơn so với chỉ vibe coding như trước.

## 6. Thú thật file này em cũng nhờ chat gpt chau chuốt lại, nếu tự viết sẽ lủng củng hơn rất nhiều.