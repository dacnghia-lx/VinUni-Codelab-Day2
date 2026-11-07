# 🔍 Task 01 — AI Product Scoping (Vin Smart Future)

---

## 📌 Phase 1 — SCAN: Danh sách 6 bài toán vận hành (4 Lenses)

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
| :--- | :--- | :--- | :--- |
| **1** | **Xanh SM** | **Pain từ người khác** | Điểm đón gợi ý trên app thường lệch (sai cổng/tòa nhà), khiến tài xế và khách mất thời gian tìm nhau, phải gọi điện xác nhận. |
| **2** | **Vinhomes** | **AI-upgrade** | Khiếu nại của cư dân trên app Resident được phân loại và chuyển bộ phận thủ công, phản hồi rập khuôn, thời gian chờ dài (~12 giờ). |
| **3** | **Vinmec** | **Tốn thời gian** | Bác sĩ mất 20–30 phút/bệnh nhân để tổng hợp và viết tóm tắt hồ sơ xuất viện thủ công. |
| **4** | **VinFast** | **Lặp lại** | Nhân viên tốn nhiều thời gian đối chiếu hóa đơn sạc điện với dữ liệu trạm sạc đối tác hằng tuần bằng bảng tính. |
| **5** | **Vinpearl / VinWonders** | **AI-upgrade** | Chatbot CSKH hỗ trợ đặt vé/đổi vé trả lời máy móc, nhiều ca phức tạp phải chuyển sang nhân viên tư vấn thủ công. |
| **6** | **Xanh SM** | **Tốn thời gian** | Tóm tắt lý do khách hủy chuyến từ ghi chú tài xế và cuộc gọi ghi âm để tìm ra lỗi hệ thống định vị/điều vận. |

> 🎯 **Top 3 bài toán được chọn để làm Quick Card:** Card #1 (Xanh SM), Card #2 (Vinhomes), Card #3 (Vinmec).

---

## 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

### 📋 QUICK PROBLEM CARD #1 — Xanh SM: Gợi ý điểm đón khách chính xác

| Hạng mục | Nội dung chi tiết |
| :--- | :--- |
| **Bài toán (1 câu)** | Điểm đón gợi ý trên app thường bị lệch (sai cổng/tòa nhà), khiến tài xế và khách mất thời gian tìm nhau. |
| **Công ty thành viên** | **Xanh SM** |
| **Ai đang đau (Actor)** | Tài xế (mất thời gian, giảm số cuốc) & Khách hàng (chờ lâu, dễ hủy chuyến). |
| **Workflow thủ công** | 1. Khách đặt xe, ghim điểm đón <br> 2. Tài xế chạy tới theo định vị <br> 3. Lệch vị trí / sai cổng <br> 4. Gọi điện xác nhận <br> 5. Tìm nhau và đón khách. |
| **Bước lỗi / tốn thời gian nhất** | Bước 3 & 4 (⏱ ~5 phút/chuyến bị lệch) |
| **AI nhảy vào hỗ trợ ở bước nào?** | Bước 1: Gợi ý điểm đón chuẩn dựa trên lịch sử các điểm đón thành công tại khu vực. |
| **Metric đo thành công (Có số)** | - Giảm tỷ lệ chuyến phải gọi điện xác nhận từ **~30% xuống ~10%**.<br>- Giảm thời gian chờ đón trung bình từ **5 phút xuống 2 phút**. |
| **Quick Architecture** | **[x] Rule / Spatial ML (Gom cụm lịch sử GPS)** |

---

### 📋 QUICK PROBLEM CARD #2 — Vinhomes: Phân loại & nháp phản hồi khiếu nại cư dân

| Hạng mục | Nội dung chi tiết |
| :--- | :--- |
| **Bài toán (1 câu)** | Khiếu nại cư dân trên app Resident được phân loại thủ công, chuyển tiếp chậm (~12h) và phản hồi rập khuôn. |
| **Công ty thành viên** | **Vinhomes** |
| **Ai đang đau (Actor)** | Nhân viên CSKH / Ban quản lý (quá tải ticket) & Cư dân (chờ lâu, phản hồi không sát). |
| **Workflow thủ công** | 1. Cư dân gửi khiếu nại trên app <br> 2. CSKH đọc và gán nhãn <br> 3. Chuyển bộ phận liên quan <br> 4. Soạn văn bản phản hồi <br> 5. Gửi và theo dõi. |
| **Bước lỗi / tốn thời gian nhất** | Bước 2 & 3: Phân loại sai, chuyển nhầm bộ phận (⏱ ~30 phút/ticket trước khi có người xử lý). |
| **AI nhảy vào hỗ trợ ở bước nào?** | - Bước 2: Tự động phân loại, tóm tắt ý chính.<br>- Bước 4: Nháp phản hồi chuẩn hóa.<br>|
| **Metric đo thành công (Có số)** | - Giảm thời gian phản hồi đầu tiên từ **~12 giờ xuống dưới 2 giờ**.<br>- Tỷ lệ phân loại đúng nhóm ticket đạt **≥ 90%**. |
| **Quick Architecture** | **[x] LLM (kèm Rule định tuyến)** |

---

### 📋 QUICK PROBLEM CARD #3 — Vinmec: Tóm tắt hồ sơ xuất viện

| Hạng mục | Nội dung chi tiết |
| :--- | :--- |
| **Bài toán (1 câu)** | Bác sĩ tốn 20–30 phút/bệnh nhân để viết tóm tắt hồ sơ xuất viện thủ công. |
| **Công ty thành viên** | **Vinmec** |
| **Ai đang đau (Actor)** | Bác sĩ điều trị (quá tải thủ tục hành chính) & Bệnh nhân (chờ đợi làm thủ tục xuất viện). |
| **Workflow thủ công** | 1. Tổng hợp diễn tiến bệnh, xét nghiệm, đơn thuốc <br> 2. Viết tóm tắt hồ sơ xuất viện <br> 3. Rà soát lại thông tin <br> 4. Ký và lưu hệ thống EMR. |
| **Bước lỗi / tốn thời gian nhất** | Bước 1 & 2 (⏱ ~20 phút/lượt) |
| **AI nhảy vào hỗ trợ ở bước nào?** | Bước 1 & 2: Tự động tạo bản nháp tóm tắt từ dữ liệu hồ sơ có sẵn trên EMR (Bác sĩ bắt buộc rà soát, duyệt và ký). |
| **Metric đo thành công (Có số)** | - Giảm thời gian viết tóm tắt từ **~25 phút xuống ~8 phút**.<br>- **0 lỗi sai thuốc / liều lượng** trong bản nháp sau bước rà soát. |
| **Quick Architecture** | **[x] LLM (Information Extraction / Summarization)** |

---

## 💡 Lý do lựa chọn bài toán để đào sâu (Deep-Dive Decision)

**Đề xuất lựa chọn Card #2 (Vinhomes — Khiếu nại cư dân) để làm các bước tiếp theo:**
1. **Khối lượng dữ liệu lớn & Đơn giản hóa được:** Tần suất phản ánh từ cư dân Vinhomes rất cao, quy trình xử lý văn bản cực kỳ phù hợp với thế mạnh trích xuất/tóm tắt/soạn thảo của LLM.
2. **Kiểm soát rủi ro an toàn:** Rủi ro được kiểm soát chặt chẽ thông qua cơ chế phân luồng theo luật (Rule-based routing) và bắt buộc có người duyệt (Human-in-the-loop) trước khi gửi.
3. **So sánh với 2 bài toán còn lại:**
   * **Card #1 (Xanh SM):** Bản chất thiên về Rule Engine kết hợp Spatial Analytics/Clustering hơn là bài toán ưu tiên dùng LLM.
   * **Card #3 (Vinmec):** Liên quan trực tiếp đến an toàn y khoa, rủi ro ảo giác (hallucination) cao, đòi hỏi quy trình kiểm duyệt và bộ dữ liệu y tế chuyên sâu phức tạp hơn ở giai đoạn prototype này.