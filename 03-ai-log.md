# AI Log & Personal Reflection

## 1. Mục tiêu sử dụng AI

Trong bài lab này, AI được sử dụng như một công cụ tham khảo để làm rõ yêu cầu
của repo, gợi ý cách chia rubric và phản biện ranh giới cho prompt prototype.
Các gợi ý sau đó được đối chiếu với worksheet, README và autograder trước khi
quyết định phạm vi, tiêu chí an toàn và nội dung cuối cùng.

Câu trả lời của AI không được coi là nguồn sự thật. Những metric chưa có dữ
liệu thực tế đều được ghi là giả định cho pilot, thay vì trình bày như số liệu
vận hành của doanh nghiệp.

## 2. Nhật ký tương tác

### Lần 1 — Giới hạn phạm vi sửa code

**Mục tiêu:** Hoàn thiện prototype mà không làm thay đổi code base ngoài phần
bài tập được giao.

**Cách điều chỉnh yêu cầu:** Phạm vi được giới hạn ở hai vùng TODO trong
`starter-code/prompt_prototype.py`; autograder, dependency và danh sách test có
sẵn không bị thay đổi.

**Cách sử dụng AI:** Các gợi ý được tham khảo để xây dựng system prompt, sau đó
bốn ranh giới phù hợp với bài toán được giữ lại: mọi output phải bắt đầu bằng
`[DRAFT_ONLY]`; khi pin dưới 5% phải dùng `dispatch_mobile_charger` và không đề
xuất trạm xa hơn 5 km; mọi hành động phải được con người phê duyệt. Lời gọi
`google-genai` cũng được kiểm tra để lấy API key từ biến môi trường thay vì ghi
trực tiếp vào code.

**Quyết định:** Kiến trúc Rule + LLM Feature được chọn thay vì agent tự trị.
Với tình huống liên quan an toàn xe, LLM chỉ nên soạn bản nháp; điều phối viên
vẫn là người quyết định và gửi thông tin.

### Lần 2 — Kiểm thử và phản biện kết quả

**Mục tiêu:** Không kết luận code đúng chỉ vì nhìn hợp lý.

**Cách sử dụng AI:** AI được dùng để hỗ trợ xây dựng phép kiểm tra theo chu trình
fail–pass và phân tích kết quả. Trước khi điền TODO, kiểm tra thất bại vì
`SYSTEM_PROMPT` còn template. Sau khi sửa, fake Gemini client xác nhận đúng
model, user input, system instruction và giá trị trả về mà không gọi mạng. Ba
kiểm tra tĩnh của autograder đều đạt.

**Điểm quan trọng rút ra:** Fake client chỉ chứng minh phần kết nối code, không
chứng minh Gemini thật sẽ luôn tuân thủ prompt. Safety hiện vẫn phụ thuộc vào
model. Nếu phát triển thành hệ thống thật, cần thêm hậu kiểm bằng code: parse
output, xác minh tag, schema JSON, mức pin, khoảng cách và chặn action không hợp
lệ trước khi hiển thị cho điều phối viên.

### Lần 3 — Phát hiện vấn đề môi trường

Khi thử import SDK thật, môi trường hiện tại báo `ModuleNotFoundError` vì
`google-genai` chưa được cài. Source code và fake client hoạt động không đồng
nghĩa môi trường đã sẵn sàng gọi API. Sau khi đối chiếu `requirements.txt`,
package được xác nhận là đã khai báo; bước còn lại là cài dependency trong
virtual environment và cung cấp API key qua biến môi trường. API key không được
đưa vào source hoặc file Markdown.

## 3. AI trả lời chưa đủ hoặc có nguy cơ gây hiểu nhầm ở đâu?

### 3.1. Có xu hướng coi metric ví dụ là dữ liệu thật

Các con số như 15 phút xử lý hay mục tiêu dưới 3 phút xuất hiện trong bài mẫu.
Nếu sao chép trực tiếp, người đọc có thể hiểu nhầm đây là số liệu chính thức
của Xanh SM. Cách trình bày được sửa thành “baseline giả định cho pilot” và yêu
cầu đo lại bằng log vận hành thực tế.

### 3.2. Prompt safety chưa phải enforcement tuyệt đối

AI có thể viết một system prompt rất chặt, nhưng LLM vẫn là hệ thống xác suất.
Vì vậy câu “bắt buộc” trong prompt không tương đương với kiểm soát cứng bằng
code. Human-in-the-loop và fallback thủ công được giữ lại; AI không có quyền
gửi tin hoặc tự điều xe.

### 3.3. Tài liệu repo có mâu thuẫn nhỏ

Chỉ khi đối chiếu nhiều file, sự khác nhau giữa yêu cầu 2 và 3 adversarial tests
mới được phát hiện. Bài học rút ra là không chỉ đọc README hoặc tin vào một câu
trả lời tổng hợp; worksheet, starter code và autograder phải được kiểm tra cùng
lúc.

### 3.4. Kiểm tra pass chưa đồng nghĩa chạy production được

Các kiểm tra tĩnh có thể pass dù máy chưa cài SDK hoặc chưa có API key. Vì vậy,
ba mức được tách rõ: cú pháp/source hợp lệ, contract với fake client hợp lệ, và
live API chưa được xác minh. Cách ghi này trung thực hơn việc chỉ báo “all tests
passed”.

## 4. Prompt và cách làm đã được làm rõ như thế nào?

Hai ví dụ dưới đây là **bản diễn giải minh họa** cho sự khác nhau giữa một yêu
cầu chung chung và một yêu cầu có ranh giới; chúng không phải trích dẫn nguyên
văn hoặc lịch sử chat theo thứ tự thời gian.

### Ví dụ yêu cầu chưa đủ rõ (minh họa)

> Hãy viết prompt cho trợ lý điều phối Xanh SM và giúp tài xế tìm trạm sạc.

Prompt này quá rộng: không nói ai phê duyệt, không có ngưỡng an toàn và không
quy định output.

### Ví dụ yêu cầu sau khi thêm ranh giới (minh họa)

> Bạn là dispatcher co-pilot, chỉ tạo bản nháp. Mọi phản hồi phải bắt đầu bằng
> `[DRAFT_ONLY]`. Nếu pin dưới 5%, phải chọn `dispatch_mobile_charger` và không
> đề xuất trạm xa hơn 5 km. Output gồm action, reason,
> requires_human_approval và message_draft. Không được tự gửi hoặc tuyên bố đã
> thực hiện hành động.

Phiên bản sau tốt hơn vì xác định rõ vai trò, điều kiện cấm, hành vi fallback,
định dạng đầu ra và điểm con người chịu trách nhiệm.

## 5. Ranh giới khi làm việc với AI

1. Không cung cấp hoặc ghi API key vào code và tài liệu.
2. Không cho AI sửa ngoài file/vùng đã thống nhất khi chưa kiểm tra phạm vi.
3. Không dùng số liệu do AI gợi ý như dữ kiện thật nếu chưa có nguồn hoặc log.
4. Không cho LLM tự gửi tin nhắn, tự điều xe hoặc thay thế người vận hành.
5. Không đánh giá kết quả chỉ bằng cảm giác; phải đối chiếu rubric, chạy
   autograder và đọc diff.
6. Khi AI và tài liệu mâu thuẫn, ưu tiên yêu cầu chính thức và ghi rõ điểm chưa
   chắc chắn thay vì tự suy đoán.

## 6. Reflection cá nhân

Giá trị rõ nhất của AI là rút ngắn thời gian đọc repo và biến một yêu cầu an
toàn còn chung chung thành các rule có thể kiểm tra. Tuy nhiên, phần có giá trị
nhất không phải là câu trả lời đầu tiên của AI mà là quá trình đặt lại câu hỏi:
phạm vi được sửa là gì, metric nào chỉ là giả định, điều gì phải do con người
quyết định, và một test pass thực sự chứng minh được điều gì.

Sau bài lab, cách nhìn thay đổi từ “dùng AI để tạo đáp án” sang “dùng AI để tạo
phương án rồi kiểm chứng”. Với bài toán vận hành có rủi ro, cần ưu tiên scope
hẹp, rule cứng cho điều kiện an toàn, LLM cho tác vụ ngôn ngữ, và luôn có
human-in-the-loop cùng fallback. Nội dung cuối cùng cần được đọc lại, xác minh
và giải thích thay vì chuyển nguyên câu trả lời của AI thành quyết định vận
hành.

## 7. Mức độ sử dụng AI và trách nhiệm cá nhân

AI được dùng để tham khảo cấu trúc, phác thảo nội dung ban đầu và hỗ trợ một số
bước kiểm thử. Sau đó, yêu cầu được đối chiếu, từng phần được đọc lại, các khẳng
định chưa có căn cứ được loại bỏ và nội dung được điều chỉnh theo rubric. Trách
nhiệm cá nhân nằm ở việc xác định giới hạn công việc, lựa chọn bài toán, kiểm
tra các mâu thuẫn, xem kết quả test và hoàn thiện bản nộp cuối cùng.
