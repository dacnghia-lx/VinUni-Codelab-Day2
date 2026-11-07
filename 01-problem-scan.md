# Individual Problem Scan & Quick Assessment

## 1. Bối cảnh và cách tôi chọn bài toán

Tôi đặt mình vào vai trò AI Product Engineer tại Vin Smart Future và ưu tiên
những quy trình có ba đặc điểm: đang được xử lý thủ công nhiều lần, có đầu ra
đo được, và có thể đặt ranh giới rõ ràng để AI không tự quyết ở bước rủi ro.
Toàn bộ workflow, pain point, điểm xếp hạng, khả năng tiếp cận dữ liệu và các
con số trong bài là **giả thuyết mô phỏng ban đầu để thiết kế pilot**, được xây
dựng từ worksheet và inspiration kit của bài lab; chúng không phải quan sát
thực địa hay số liệu vận hành đã được xác thực của các công ty. Trước khi triển
khai, nhóm dự án cần phỏng vấn người trực tiếp vận hành, kiểm tra hệ thống hiện
có và đo baseline từ log thực tế.

## 2. Phase 1 — SCAN: Danh sách cơ hội

| # | Công ty | Lens chính | Giả thuyết bài toán | Vì sao đáng xem xét |
|---:|---|---|---|---|
| 1 | Xanh SM | Tốn thời gian / Stakeholder Pain | Điều phối viên phải xử lý thủ công khi xe điện gần hết pin giữa hành trình: xác định vị trí, tìm trạm phù hợp và soạn hướng dẫn cho tài xế. | Quy trình xảy ra trong tình huống gấp, nhiều handoff và có rủi ro an toàn nếu hướng dẫn sai. |
| 2 | Vinhomes | Lặp lại | Phản ánh của cư dân phải được đọc và chuyển thủ công đến đúng ban quản lý, tòa nhà và nhóm kỹ thuật. | Nội dung có nhiều cách diễn đạt nhưng nhóm xử lý đầu ra tương đối ổn định. |
| 3 | VinFast | AI-upgrade | Cố vấn dịch vụ phải đọc mô tả tiếng Việt đời thường của khách để xác định nhóm lỗi xe ban đầu. | Ngôn ngữ khách hàng mơ hồ; việc chuẩn hóa triệu chứng trước khi tiếp nhận có thể giảm thời gian hỏi lại. |
| 4 | Vinpearl | Stakeholder Pain | Quản lý phải đọc thủ công nhiều đánh giá từ các nền tảng để phát hiện phàn nàn khẩn cấp. | Review phân tán, khối lượng lớn và dễ bỏ sót sự cố cần phản hồi sớm. |
| 5 | Vinmec | Tốn thời gian | Nhân viên y tế tổng hợp nhiều nguồn thông tin để soạn bản nháp tóm tắt xuất viện dễ hiểu cho bệnh nhân. | Có thể giảm thao tác tổng hợp, nhưng đây là lĩnh vực rủi ro cao và bắt buộc bác sĩ kiểm duyệt. |

## 3. Tiêu chí chọn Top 3

Tôi đánh giá sơ bộ theo thang 1–5. Điểm cao không đồng nghĩa với việc nên tự
động hóa hoàn toàn; tiêu chí rủi ro dùng điểm cao khi rủi ro **dễ kiểm soát**
bằng rule, human-in-the-loop hoặc fallback.

| Bài toán | Tần suất/lặp lại | Đo được kết quả | Dữ liệu đầu vào có thể tiếp cận | Rủi ro có thể kiểm soát | Tổng |
|---|---:|---:|---:|---:|---:|
| Xanh SM — sự cố pin thực địa | 5 | 5 | 4 | 4 | **18** |
| Vinhomes — phân loại phản ánh | 5 | 4 | 4 | 4 | **17** |
| VinFast — chuẩn hóa mô tả lỗi | 4 | 4 | 3 | 4 | **15** |
| Vinpearl — phân tích review | 4 | 4 | 4 | 3 | 15 |
| Vinmec — tóm tắt xuất viện | 4 | 4 | 3 | 2 | 13 |

Ba bài toán được đưa vào Quick Assessment là Xanh SM, Vinhomes và VinFast.
Vinpearl có cùng điểm với VinFast nhưng được xếp sau vì tác động chủ yếu ở
khâu theo dõi back-office. Vinmec chưa được chọn vì cần quy trình quản trị dữ
liệu y tế và kiểm định chuyên môn nghiêm ngặt hơn trước khi thử nghiệm.

---

## 4. Phase 2 — QUICK-ASSESS

### Quick Problem Card #1 — Xanh SM xử lý sự cố pin thực địa

**Bài toán một câu:** Điều phối viên mất nhiều thời gian tổng hợp vị trí xe,
mức pin và trạm sạc để đưa ra hướng dẫn an toàn cho tài xế.

**Actor:** Điều phối viên trung tâm vận hành Xanh SM; người chịu tác động trực
tiếp là tài xế và hành khách đang chờ chuyến.

**Workflow hiện tại giả định:**

1. Tài xế gọi hoặc nhắn báo mức pin và tình trạng xe.
2. Điều phối viên xác minh biển số, dòng xe và vị trí GPS.
3. Điều phối viên tra cứu trạm sạc, khoảng cách và khả năng tương thích.
4. Điều phối viên soạn hướng dẫn gửi tài xế.
5. Nếu xe không thể di chuyển an toàn, điều phối viên gọi hỗ trợ lưu động.

**Bottleneck:** Bước 3–4 vì phải chuyển qua nhiều màn hình và tự tổng hợp dữ
liệu trước khi viết hướng dẫn. Baseline giả định cho pilot, tính từ lúc điều
phối viên tiếp nhận đủ thông tin ban đầu đến khi có phương án để duyệt, là 15
phút/lượt.

**AI có thể hỗ trợ:** Rule engine kiểm tra ngưỡng an toàn và khả năng tương
thích; LLM chỉ soạn bản nháp dễ hiểu từ dữ liệu đã được xác thực.

**Metric thử nghiệm:**

- Giảm thời gian từ lúc tiếp nhận đủ thông tin đến khi có phương án ban đầu từ
  baseline giả định 15 phút xuống dưới 3 phút.
- 100% trường hợp pin dưới 5% kích hoạt luồng
  `dispatch_mobile_charger`, không đề xuất trạm xa hơn 5 km.
- 100% tin nhắn có trạng thái `[DRAFT_ONLY]` và được điều phối viên duyệt.
- Sau pilot, đo tỷ lệ phương án được duyệt mà không cần sửa lớn; mục tiêu ban
  đầu tối thiểu 85%.

**Quick Architecture:** Rule/State Machine + LLM Feature, không dùng agent tự
trị.

**Operational Boundary:** AI không tự gửi tin, không tự điều xe, không bịa dữ
liệu trạm sạc. Khi thiếu dữ liệu hoặc mức pin dưới 5%, hệ thống chuyển cho điều
phối viên và ưu tiên hỗ trợ lưu động.

**Fallback:** Điều phối viên quay về quy trình tra cứu và soạn tin thủ công.

---

### Quick Problem Card #2 — Vinhomes phân loại phản ánh cư dân

**Bài toán một câu:** Phản ánh của cư dân được viết tự do nên nhân viên phải
đọc, phân loại và chuyển từng yêu cầu đến đúng đơn vị xử lý.

**Actor:** Nhân viên chăm sóc cư dân và ban quản lý từng khu/tòa nhà.

**Workflow hiện tại giả định:**

1. Cư dân gửi nội dung, hình ảnh và thông tin căn hộ qua ứng dụng.
2. Nhân viên đọc nội dung và xác định loại sự cố.
3. Nhân viên kiểm tra khu vực, mức độ khẩn cấp và đơn vị phụ trách.
4. Yêu cầu được chuyển sang kỹ thuật, an ninh, vệ sinh hoặc kế toán.
5. Nhân viên theo dõi trạng thái và phản hồi cư dân.

**Bottleneck:** Bước 2–3, đặc biệt khi nội dung ngắn, có lỗi chính tả hoặc chứa
nhiều vấn đề trong cùng một phản ánh. Baseline giả định, tính từ lúc ticket vào
hàng chờ đến khi hoàn tất phân loại và chọn đơn vị xử lý, là 8 phút/yêu cầu.

**AI có thể hỗ trợ:** LLM trích xuất loại vấn đề, vị trí và mức độ khẩn cấp;
rule engine ánh xạ kết quả sang danh sách đơn vị đã được ban quản lý phê duyệt.

**Metric thử nghiệm:**

- Giảm thời gian từ lúc ticket vào hàng chờ đến khi có kết quả phân loại từ
  baseline giả định 8 phút xuống dưới 2 phút.
- Đạt tối thiểu 90% độ chính xác top-1 trên bộ dữ liệu đã được gán nhãn.
- 100% phản ánh liên quan an toàn, cháy nổ hoặc sức khỏe phải được gắn cờ khẩn
  cấp để con người duyệt ngay.

**Quick Architecture:** LLM Feature + Rule Router.

**Operational Boundary:** AI không được tự kết luận trách nhiệm, hứa thời hạn
bồi thường hoặc đóng ticket. Các trường hợp nhạy cảm và confidence thấp phải
chuyển nhân viên.

**Fallback:** Đưa ticket vào hàng chờ “Chưa xác định” để nhân viên phân loại.

---

### Quick Problem Card #3 — VinFast chuẩn hóa mô tả lỗi ban đầu

**Bài toán một câu:** Mô tả triệu chứng xe bằng ngôn ngữ đời thường chưa đủ
cấu trúc để cố vấn dịch vụ nhanh chóng xác định nhóm kiểm tra phù hợp.

**Actor:** Cố vấn dịch vụ VinFast; người cung cấp đầu vào là chủ xe hoặc tài
xế.

**Workflow hiện tại giả định:**

1. Khách mô tả hiện tượng qua điện thoại, ứng dụng hoặc tại xưởng.
2. Cố vấn hỏi lại về âm thanh, vị trí, thời điểm và điều kiện xuất hiện.
3. Cố vấn ghi chú lại bằng thuật ngữ thống nhất.
4. Cố vấn chọn nhóm kỹ thuật cần kiểm tra và sắp lịch.
5. Kỹ thuật viên chẩn đoán trực tiếp bằng quy trình chuyên môn.

**Bottleneck:** Bước 2–3 do mô tả không chuẩn, ví dụ “kêu cụp cụp”, “xe rung”
hoặc “đèn báo lúc có lúc không”. Baseline giả định, tính từ lúc bắt đầu hỏi
khách đến khi hoàn tất bản ghi triệu chứng đã chuẩn hóa, là 12 phút/lượt.

**AI có thể hỗ trợ:** LLM chuyển mô tả thành bản tóm tắt có cấu trúc, đề xuất
câu hỏi còn thiếu và nhóm kiểm tra tham khảo dựa trên tài liệu đã được duyệt.

**Metric thử nghiệm:**

- Giảm thời gian từ lúc bắt đầu hỏi khách đến khi hoàn tất bản ghi chuẩn hóa từ
  baseline giả định 12 phút xuống dưới 4 phút.
- Ít nhất 90% phiếu tiếp nhận có đủ vị trí, thời điểm, điều kiện xuất hiện và
  mức độ ảnh hưởng.
- Không có trường hợp nào AI tự kết luận xe an toàn để tiếp tục vận hành.

**Quick Architecture:** Retrieval + LLM Feature; không dùng agent tự thực thi.

**Operational Boundary:** Output chỉ là thông tin tiếp nhận, không phải chẩn
đoán kỹ thuật. AI không xóa mã lỗi, không hướng dẫn tự sửa hạng mục nguy hiểm
và không thay thế kỹ thuật viên.

**Fallback:** Nếu mô tả thiếu hoặc confidence thấp, hệ thống đưa ra danh sách
câu hỏi để cố vấn tiếp tục phỏng vấn khách hàng.

## 5. Lựa chọn cá nhân

Tôi chọn bài toán **Xanh SM xử lý sự cố pin thực địa** để làm prompt prototype.
Bài toán này có actor, workflow, metric và ranh giới an toàn rõ hơn hai phương
án còn lại. Kiến trúc phù hợp là kết hợp rule với một LLM feature: rule quyết
định các điều kiện an toàn, còn LLM chỉ tạo bản nháp cho con người duyệt. Tôi
không chọn agent tự trị vì một quyết định sai có thể khiến xe hết pin giữa
đường hoặc gửi hướng dẫn chưa được kiểm tra cho tài xế.

