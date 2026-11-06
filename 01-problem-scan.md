# Phase 1 — SCAN: AI Opportunities at Vinhomes

## 1. Bối cảnh

Trong bài lab này, em lựa chọn lĩnh vực quản lý và
vận hành khu đô thị Vinhomes để tìm kiếm các cơ hội
ứng dụng AI.

Tôi tập trung vào quy trình tiếp nhận, phân loại và
xử lý phản ánh của cư dân.

Các vấn đề dưới đây là giả thuyết ban đầu, cần được
kiểm chứng bằng khảo sát và dữ liệu vận hành thực tế.

## 2. Danh sách 5 vấn đề

| # | Subsidiary | Lens | Problem |
|---|---|---|---|
| 1 | Vinhomes | Repetitive | Nhân viên phải đọc và phân loại thủ công các phản ánh của cư dân về điện, nước, vệ sinh và an ninh. |
| 2 | Vinhomes | Time-consuming | Nhân viên mất thời gian tổng hợp lịch sử phản ánh của cư dân từ nhiều lần liên hệ. |
| 3 | Vinhomes | AI-upgrade | Hệ thống phản hồi tự động khó xử lý các câu hỏi phức tạp về phí dịch vụ, hợp đồng và quy định cư dân. |
| 4 | Vinhomes | Stakeholder Pain | Cư dân khó theo dõi tiến độ xử lý khi phản ánh chuyển qua nhiều bộ phận. |
| 5 | Vinhomes | Repetitive | Nhân viên phải tổng hợp báo cáo hằng ngày về số lượng phản ánh và các vấn đề tồn đọng. |

## 3. Ba bài toán được lựa chọn

1. Phân loại phản ánh cư dân tự động.
2. Tổng hợp lịch sử khiếu nại kéo dài.
3. Theo dõi và hỗ trợ giải quyết phản ánh qua nhiều bộ phận.

## 4. Lý do lựa chọn

Ba bài toán được lựa chọn vì có thể tác động trực tiếp
đến hiệu suất làm việc của nhân viên và trải nghiệm
của cư dân.

Các bài toán cũng phù hợp để so sánh các mức giải pháp
Rule-based, LLM Feature và Agentic Loop.

Ở giai đoạn tiếp theo, tôi sẽ phân tích quy trình hiện tại,
xác định bottleneck và đề xuất các chỉ số đánh giá cụ thể.

---

# Phase 2 — QUICK-ASSESS

## Quick Problem Card #1

### 1. Bài toán
Nhân viên Vinhomes phải đọc và phân loại thủ công
các phản ánh của cư dân về điện, nước, vệ sinh
và an ninh.

### 2. Công ty
Vinhomes

### 3. Actor
Nhân viên chăm sóc khách hàng và ban quản lý tòa nhà.

### 4. Current Workflow
1. Cư dân gửi phản ánh qua ứng dụng hoặc tổng đài.
2. Nhân viên tiếp nhận và đọc nội dung.
3. Nhân viên xác định loại vấn đề.
4. Chuyển phản ánh đến bộ phận phụ trách.
5. Bộ phận tiếp nhận và xử lý.

### 5. Bottleneck
Việc đọc và phân loại phản ánh thủ công có thể
gây chậm trễ hoặc chuyển sai bộ phận.

Giả định thời gian xử lý: 8 phút/yêu cầu.

### 6. AI Solution
Sử dụng LLM để phân tích nội dung phản ánh,
phân loại vấn đề và đề xuất bộ phận xử lý.

Nhân viên kiểm tra trước khi chuyển yêu cầu.

### 7. Success Metric
Mục tiêu giảm thời gian phân loại từ 8 phút
xuống dưới 2 phút/yêu cầu.

Độ chính xác phân loại mục tiêu: >= 95%.

### 8. Quick Architecture
LLM Feature

### 9. Operational Boundary
AI không được tự ý đóng khiếu nại hoặc đưa ra
cam kết tài chính, pháp lý với cư dân.

---

## Quick Problem Card #2

### 1. Bài toán
Nhân viên mất nhiều thời gian tổng hợp lịch sử
khiếu nại của một cư dân từ nhiều lần liên hệ.

### 2. Công ty
Vinhomes

### 3. Actor
Nhân viên chăm sóc khách hàng và quản lý vận hành.

### 4. Current Workflow
1. Tiếp nhận khiếu nại mới.
2. Tra cứu các yêu cầu trước đó.
3. Đọc lịch sử trao đổi.
4. Tổng hợp thông tin liên quan.
5. Đề xuất phương án xử lý.

### 5. Bottleneck
Thông tin nằm trong nhiều yêu cầu khác nhau,
khiến nhân viên mất thời gian tổng hợp.

Giả định thời gian xử lý: 15 phút/trường hợp.

### 6. AI Solution
Sử dụng LLM kết hợp truy xuất dữ liệu được
phân quyền để tóm tắt lịch sử khiếu nại.

AI hiển thị nguồn thông tin để nhân viên kiểm tra.

### 7. Success Metric
Mục tiêu giảm thời gian tổng hợp từ 15 phút
xuống dưới 5 phút/trường hợp.

Tỷ lệ thông tin quan trọng được trích xuất đúng
đạt ít nhất 95%.

### 8. Quick Architecture
LLM Feature kết hợp truy xuất dữ liệu.

### 9. Operational Boundary
AI chỉ được truy xuất hồ sơ mà nhân viên có quyền xem.

Không tự tạo thông tin hoặc thay đổi hồ sơ gốc.

---

## Quick Problem Card #3

### 1. Bài toán
Cư dân khó theo dõi tiến độ xử lý khi phản ánh
phải chuyển qua nhiều bộ phận.

### 2. Công ty
Vinhomes

### 3. Actor
Cư dân, nhân viên CSKH và các bộ phận kỹ thuật.

### 4. Current Workflow
1. Cư dân gửi phản ánh.
2. CSKH tiếp nhận.
3. CSKH chuyển yêu cầu đến bộ phận phụ trách.
4. Bộ phận phụ trách cập nhật tiến độ.
5. CSKH phản hồi lại cư dân.

### 5. Bottleneck
Quá trình trao đổi giữa nhiều bộ phận có thể
làm chậm cập nhật trạng thái và phản hồi.

Giả định thời gian tổng hợp và cập nhật:
10 phút/lần.

### 6. AI Solution
Xây dựng Agent hỗ trợ theo dõi trạng thái yêu cầu.

Agent có thể:
- Truy xuất trạng thái từ hệ thống quản lý.
- Kiểm tra yêu cầu đang nằm ở bộ phận nào.
- Phát hiện trường hợp quá hạn theo quy tắc SLA.
- Đề xuất nhắc nhở nhân viên phụ trách.
- Giải thích tiến độ cho cư dân dựa trên dữ liệu thật.

Những hành động quan trọng phải được con người
phê duyệt trước khi thực hiện.

### 7. Success Metric
Mục tiêu giảm thời gian tổng hợp trạng thái từ
10 phút xuống dưới 2 phút/lần.

Mục tiêu 95% yêu cầu có trạng thái cập nhật đúng
và có thể truy xuất.

### 8. Quick Architecture
Agentic Loop

### 9. Operational Boundary
Agent không được tự ý:
- Đóng khiếu nại.
- Thay đổi kết quả xử lý.
- Cam kết thời hạn ngoài dữ liệu được cung cấp.
- Truy cập thông tin cư dân không được phân quyền.

Các quyết định quan trọng cần nhân viên phê duyệt.

---

## Kết luận Phase 2

Sau khi so sánh ba bài toán, tôi nhận thấy bài toán
phân loại phản ánh cư dân tự động phù hợp nhất
để bắt đầu thử nghiệm.

Lý do:
1. Quy trình tương đối rõ ràng.
2. Có thể đo lường thời gian và độ chính xác.
3. Phạm vi thử nghiệm có thể giới hạn.
4. LLM Feature có thể hỗ trợ xử lý ngôn ngữ tự nhiên.
5. Con người vẫn kiểm soát quyết định cuối cùng.

Các chỉ số thời gian và độ chính xác ở trên là
giả định phục vụ bước scoping, chưa phải dữ liệu
vận hành thực tế của Vinhomes.

Cần thu thập dữ liệu thực tế trước khi đưa ra
quyết định triển khai.