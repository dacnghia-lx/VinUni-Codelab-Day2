# 01 – Problem Scan & Quick Assessment
## Lab 02: AI Product Scoping – Vin Smart Future

**Phạm vi:** Bài làm cá nhân minh họa, chọn bài toán điều phối sự cố pin tại Xanh SM để phân tích sâu.  
**Lưu ý về dữ liệu:** Các con số thời gian, số lượng sự cố và mục tiêu bên dưới là **giả định phục vụ bài tập**, không phải dữ liệu nội bộ đã được xác minh của Vingroup. Cần đo baseline thực tế trước khi triển khai.

---

## 1. Phase 1 – SCAN: Danh sách cơ hội

| # | Công ty | Lens | Bài toán / bottleneck | Người chịu ảnh hưởng | Cơ hội cải thiện |
|---|---|---|---|---|---|
| 1 | Xanh SM | Time-consuming | Dispatcher phải tra cứu GPS, kiểm tra trạm sạc tương thích/còn hoạt động và tự soạn hướng dẫn khi tài xế báo pin yếu. | Dispatcher, tài xế, khách chờ chuyến | Tự động tổng hợp dữ liệu đáng tin cậy và tạo bản nháp hướng dẫn. |
| 2 | VinFast | Repetitive | Nhân viên vận hành phải đối chiếu giao dịch sạc, hóa đơn và dữ liệu phiên sạc từ nhiều nguồn. | Nhân viên tài chính/vận hành | Rule-based matching, AI chỉ hỗ trợ giải thích các trường hợp lệch dữ liệu. |
| 3 | Vinhomes | AI-upgrade | Phản ánh cư dân đến qua nhiều kênh, nội dung tự do nên cần đọc, phân loại và chuyển đúng bộ phận. | CSKH, ban quản lý, cư dân | Phân loại văn bản và đề xuất bộ phận tiếp nhận; nhân viên duyệt trước khi tạo ticket. |
| 4 | Vinpearl / VinWonders | AI-upgrade | Nhân viên CSKH trả lời lặp lại câu hỏi về giờ hoạt động, tiện ích, quy định vé và dịch vụ. | CSKH, khách du lịch | Trợ lý hỏi đáp dựa trên kho thông tin đã xác thực (RAG), dẫn nguồn và chuyển nhân viên khi thiếu dữ liệu. |
| 5 | Vinmec | Stakeholder Pain | Nhân viên hành chính y tế mất thời gian tổng hợp thông tin hành chính từ hồ sơ để chuẩn bị bản nháp tóm tắt. | Nhân viên y tế, bệnh nhân | Hỗ trợ tóm tắt có trích nguồn; bác sĩ chịu trách nhiệm xác minh, không tự chẩn đoán/ra quyết định điều trị. |
| 6 | Xanh SM | Repetitive | Phân loại lý do hủy chuyến từ ghi chú tài xế và phản hồi khách hàng đang cần thao tác thủ công. | Nhóm phân tích vận hành | NLP gom nhóm nguyên nhân, dashboard theo xu hướng; kiểm tra mẫu thủ công. |

### Cách áp dụng 4 Lenses
- **Repetitive:** #2 và #6 có các bước đối chiếu/phân loại lặp lại.
- **Time-consuming:** #1 yêu cầu tra cứu nhiều màn hình và tổng hợp thông tin.
- **AI-upgrade:** #3 và #4 có dữ liệu văn bản/ngôn ngữ tự nhiên.
- **Stakeholder Pain:** #5 có thể gây chậm luồng công việc; do liên quan y tế nên cần giới hạn AI rất chặt.

---

## 2. Phase 2 – QUICK-ASSESS: Ba Quick Problem Cards

### Card 1 – Xanh SM: Trợ lý xử lý sự cố pin / tìm hỗ trợ sạc

| Trường | Nội dung |
|---|---|
| Bài toán | Khi tài xế báo pin yếu, dispatcher phải xác minh vị trí, mức pin, loại xe và tình trạng trạm sạc rồi hướng dẫn phương án an toàn. |
| Công ty | Xanh SM (GSM) |
| Actor | Dispatcher và tài xế. |
| Workflow hiện tại | (1) Nhận cuộc gọi/ticket → (2) tra cứu GPS và mức pin → (3) kiểm tra trạm sạc, cổng sạc, trạng thái → (4) chọn phương án → (5) soạn hướng dẫn và chuyển dispatcher duyệt/gửi. |
| Bottleneck | Tra cứu nhiều nguồn và đối chiếu tương thích; giả định 10/15 phút/lượt nằm ở bước 3–4. |
| AI hỗ trợ | Tổng hợp dữ liệu có cấu trúc, giải thích lựa chọn và soạn bản nháp tiếng Việt. Bộ quy tắc xác định trường hợp pin nguy cấp. |
| Metric | Baseline giả định 15 phút/lượt; mục tiêu trung vị dưới 3 phút/lượt. Tỷ lệ đề xuất đạt đủ điều kiện dữ liệu mục tiêu ≥98% trên tập test đã gán nhãn. |
| Architecture | Rule-based safety + LLM feature; không dùng agent tự trị. |

### Card 2 – Vinhomes: Phân loại và định tuyến phản ánh cư dân

| Trường | Nội dung |
|---|---|
| Bài toán | Phân loại phản ánh văn bản thành nhóm sự cố và đề xuất bộ phận xử lý. |
| Công ty | Vinhomes |
| Actor | Nhân viên CSKH và ban quản lý. |
| Workflow hiện tại | (1) Nhận phản ánh → (2) đọc nội dung → (3) xác định danh mục/mức độ → (4) tìm bộ phận phụ trách → (5) tạo/chuyển ticket. |
| Bottleneck | Nội dung viết tự do, thiếu thông tin và phân loại không đồng nhất. |
| AI hỗ trợ | LLM phân loại, trích xuất địa điểm/mô tả và hỏi bổ sung khi thiếu; nhân viên xác nhận trước khi chuyển ticket. |
| Metric | ≥90% ticket được đề xuất danh mục trong 10 giây; macro-F1 mục tiêu ≥0,90 trên tập đánh giá đại diện; 100% ticket khẩn cấp được con người kiểm tra. |
| Architecture | LLM feature kết hợp danh mục/rule-based routing. |

### Card 3 – Vinpearl: Trợ lý hỏi đáp dịch vụ du lịch

| Trường | Nội dung |
|---|---|
| Bài toán | Trả lời nhanh các câu hỏi lặp lại về dịch vụ, giờ hoạt động, tiện ích và chính sách vé. |
| Công ty | Vinpearl / VinWonders |
| Actor | Khách hàng và nhân viên CSKH. |
| Workflow hiện tại | (1) Khách gửi câu hỏi → (2) nhân viên xác định chủ đề → (3) tra cứu tài liệu → (4) soạn câu trả lời → (5) phản hồi/chuyển bộ phận. |
| Bottleneck | Tra cứu thông tin phân tán và cập nhật chính sách. |
| AI hỗ trợ | RAG trên tài liệu chính thức có phiên bản; trả lời kèm nguồn và ngày cập nhật. |
| Metric | ≥80% câu hỏi FAQ được phản hồi trong 10 giây; ≥95% câu trả lời được đánh giá đúng theo bộ câu hỏi chuẩn; không trả lời khi thiếu nguồn. |
| Architecture | LLM + RAG + escalation rule. |

---

## 3. So sánh và lựa chọn

| Tiêu chí | Xanh SM – Sự cố pin | Vinhomes – Ticket | Vinpearl – FAQ |
|---|---|---|---|
| Quy trình có thể mô tả rõ | Cao | Cao | Cao |
| Dữ liệu cần thiết | GPS, pin, xe, trạm sạc | Văn bản ticket, danh mục | Tài liệu dịch vụ chính thức |
| Khả năng prototype | Cao với dữ liệu giả lập | Cao | Cao |
| Rủi ro nếu AI sai | Cao: có thể khiến xe đi không an toàn | Trung bình/cao tùy mức độ sự cố | Trung bình: sai chính sách dịch vụ |
| Biện pháp kiểm soát | Rule cứng + dispatcher duyệt | Ưu tiên khẩn cấp + nhân viên duyệt | RAG, trích nguồn, chuyển người |
| Quyết định | **Chọn để Deep-Dive** | Chưa chọn | Chưa chọn |

### Lý do chọn Xanh SM
1. Có workflow tương đối cụ thể, đầu vào và đầu ra có thể xác định.
2. Có thể tách rõ tác vụ định lượng/điều kiện (Rule) khỏi tác vụ ngôn ngữ (LLM).
3. Rủi ro giúp minh họa tốt Operational Boundary, Human-in-the-loop và Fallback.
4. Có thể xây dựng prototype an toàn bằng dữ liệu giả lập mà không cần kết nối hệ thống vận hành thật.

### Vì sao chưa chọn hai bài toán còn lại?
- **Vinhomes:** Cần taxonomy thống nhất, dữ liệu ticket đã gán nhãn và quy tắc ưu tiên theo mức độ ảnh hưởng. Có thể là dự án tiếp theo.
- **Vinpearl:** RAG phù hợp nhưng cần kho tài liệu chính thức có quản lý phiên bản và quy trình cập nhật chính sách; phạm vi FAQ có thể triển khai sau khi chuẩn hóa tri thức.

---

## 4. Giả định và dữ liệu cần xác minh
- Thời gian xử lý 15 phút/lượt và 10 phút tại các bước nghẽn là giả định ban đầu.
- Cần thu thập timestamp theo từng bước từ ticket/call log để xác định median, P90 và thời gian chờ.
- Cần xác minh quyền truy cập GPS, SOC, loại xe/cổng sạc và trạng thái trạm.
- Trạng thái trạm phải có timestamp/độ mới; dữ liệu cũ không được xem là xác nhận trụ còn trống.
- Không sử dụng dữ liệu cá nhân hoặc vị trí thật trong prototype; dùng dữ liệu giả lập/đã ẩn danh.
