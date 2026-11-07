# 🔬 Task 02 — Deep-Dive Report: Phân Loại Và Phản Hồi Khiếu Nại Cư Dân Vinhomes

---

## 📌 Phase 3 — DEEP-DIVE: Phân Tích Sâu Bài Toán

### 3.1. Current-State Workflow Mapping (Quy trình hiện tại)

| Bước | Hành động | Ai làm | Thời gian | Ghi chú & Điểm nghẽn |
| :---: | :--- | :---: | :---: | :--- |
| **1** | Cư dân gửi khiếu nại trên app Vinhomes Resident | Cư dân | 0 | Văn bản tự do, có thể kèm ảnh minh họa. |
| **2** | Đọc và gán nhãn loại khiếu nại | CSKH | ~5 phút | 🔴 **Bottleneck:** Gán nhãn sai / không nhất quán. |
| **3** | 🔄 **Handoff:** Chuyển cho bộ phận chuyên trách (Kỹ thuật, An ninh, Tài chính...) | CSKH $\rightarrow$ Bộ phận | Chờ ~6–10 giờ | 🔴 **Bottleneck:** Chuyển nhầm, phải chuyển lại qua nhiều khâu. |
| **4** | Soạn văn bản phản hồi cho cư dân | CSKH | ~10 phút | 🔴 **Bottleneck:** Phản hồi rập khuôn, phải viết đi viết lại nhiều lần. |
| **5** | 🔄 Gửi phản hồi và theo dõi đến khi đóng ticket | CSKH | ~5 phút | Hoàn tất xử lý yêu cầu. |

* **Tổng thời gian xử lý thủ công của nhân viên:** ~20 phút / ticket.
* **Thời gian chờ End-to-End trước phản hồi đầu tiên:** ~12 giờ.
* **Sơ đồ trực quan quy trình:** Xem file `04-workflow-diagram.png`.

---

### 3.2. Problem Statement (6-Field Standard)

| Field | Nội dung chi tiết |
| :--- | :--- |
| **1. Actor / Operator** | Nhân viên CSKH Vinhomes xử lý ticket trên app Resident; cư dân là người nhận phản hồi. |
| **2. Current Workflow** | CSKH đọc từng khiếu nại, gán nhãn, chuyển bộ phận, tự soạn phản hồi, theo dõi thủ công qua 5 bước (~20 phút nhân công/ticket, thời gian chờ ~12 giờ). |
| **3. Bottleneck** | Bước 2–4: Gán nhãn và chuyển bộ phận sai; soạn phản hồi lặp lại, văn phong rập khuôn. |
| **4. Business Impact** | Giả định ~500 ticket/ngày: Ngốn ~165 giờ công/ngày cho xử lý ban đầu; thời gian chờ ~12 giờ làm giảm mức độ hài lòng của cư dân và tăng khiếu nại lặp. |
| **5. Success Metric** | (1) Giảm thời gian phản hồi đầu tiên từ **~12 giờ xuống dưới 2 giờ**.<br>(2) Tỷ lệ phân loại đúng nhóm ticket đạt **$\ge 90\%$**.<br>(3) Rút ngắn thời gian CSKH xử lý từ **20 phút xuống dưới 8 phút/ticket**. |
| **6. Operational Boundary** | - **Được phép:** Phân loại, tóm tắt, đề xuất bộ phận, soạn nháp phản hồi.<br>- **CẤM:** Tự động gửi phản hồi; tự hứa bồi thường/giảm phí; tư vấn pháp lý; kết luận về tranh chấp căn hộ, phí quản lý.<br>- **Cần duyệt:** Mọi phản hồi gửi cư dân (Human-in-the-loop); các ticket thuộc nhóm nhạy cảm chuyển cho con người xử lý trực tiếp. |

---

### 3.3. Future-State Flow & AI Fit (Quy trình tương lai)

#### 📊 AI-Fit Matrix: So sánh giải pháp

| Phương án | Ưu điểm | Nhược điểm | Kết luận |
| :--- | :--- | :--- | :--- |
| **Rule / State-machine** | Rẻ, dễ kiểm soát, giải thích được | Không hiểu văn bản tự do; cần liệt kê từ khóa thủ công | Dùng để định tuyến nhóm nhạy cảm (phí, pháp lý, an ninh). |
| **LLM Feature** | Hiểu văn bản tự do, tóm tắt và soạn nháp tốt | Có thể sai/bịa (hallucination); cần con người duyệt | **[X] Chọn cho phân loại, tóm tắt & nháp phản hồi.** |
| **Agentic Loop** | Tự xử lý nhiều bước phức tạp | Rủi ro cao, khó kiểm soát, chưa cần thiết cho giai đoạn này | Không chọn. |

#### 🔄 Quy trình Future-State Flow
1. Cư dân gửi khiếu nại trên ứng dụng.
2. **Rule Filter:** Kiểm tra từ khóa nhạy cảm (phí quản lý, hợp đồng, kiện tụng, an ninh khẩn cấp) $\rightarrow$ Chuyển thẳng cho người xử lý, không qua AI soạn.
3. 🔵 **AI Step:** LLM phân loại nhóm, tóm tắt ý chính, đề xuất bộ phận và soạn nháp phản hồi (luôn đính kèm thẻ `[DRAFT_ONLY]`).
4. 🔄 **Hệ thống tự định tuyến:** Ticket tự động được chuyển đến đúng bộ phận chuyên trách theo nhãn.
5. 🟢 **Human Step (HITL):** CSKH xem thư nháp, chỉnh sửa nếu cần, bấm duyệt rồi mới phát gửi cho cư dân.
6. Theo dõi đến khi đóng ticket; CSKH gắn cờ *"AI sai"* để lưu log cải thiện mô hình.

#### ↩️ Cơ chế dự phòng (Fallback Mechanism)
* LLM bị lỗi/timeout hoặc trả về độ tự tin thấp $\rightarrow$ Ticket chuyển vào hàng chờ xử lý thủ công như quy trình cũ.
* Nhãn không thuộc danh mục chuẩn hoặc chứa từ khóa nhạy cảm $\rightarrow$ Chuyển thẳng cho con người, không khởi tạo thư nháp.
* Tỷ lệ CSKH phải sửa nháp vượt quá 50% trong một tuần $\rightarrow$ Tạm dừng AI và rà soát lại System Prompt.

---

## 🏁 Phase 5 — EVALUATE: Đánh Giá Độ Sẵn Sàng & Quyết Định

### 5.1. AI Readiness Checklist

- [ ] **Có sẵn dữ liệu mẫu/logs sạch để test?** $\rightarrow$ *Chưa chắc:* Cần xuất ~1.000 ticket đã gán nhãn từ app Resident và ẩn toàn bộ thông tin cá nhân (PII) trước khi đưa vào huấn luyện/prompting.
- [x] **Rủi ro khi AI sai nằm trong tầm kiểm soát?** $\rightarrow$ *Có:* AI chỉ đóng vai trò soạn nháp, con người duyệt trước khi phát đi, các nhóm nhạy cảm được lọc bằng Rule, có cơ chế Fallback rõ ràng.
- [ ] **Stakeholders sẵn sàng đổi quy trình?** $\rightarrow$ *Cần xác nhận:* Cần làm việc và thống nhất với Trưởng phòng CSKH Vinhomes (nhân viên CSKH có thể tâm lý lo sợ bị thay thế).

### 5.2. Quyết Định Đầu Tư (Final Decision)

* **Quyết định:** **GO (Scope hẹp)** 🟢
* **Lý giải quyết định (Justification):** 
  Chọn **GO** với phạm vi hẹp: Chỉ phân loại, tóm tắt và nháp phản hồi cho nhóm khiếu nại ít rủi ro (Bảo trì, Tiện ích, Tiếng ồn), thử nghiệm Pilot tại 1 dự án đô thị trong vòng 4 tuần. 
  - **Lý do:** Bài toán có metric đo lường rất rõ ràng, khối lượng ticket lớn, bản chất phù hợp với năng lực xử lý văn bản tự do của LLM, và rủi ro được chặn hoàn toàn bởi lớp lọc Rule nhạy cảm + con người duyệt (HITL). Chi phí triển khai thấp do không sử dụng Agent phức tạp.
* **Điều kiện để mở rộng toàn hệ thống:** 
  1. Có dữ liệu baseline thật về thời gian xử lý và tỷ lệ phân loại sai trong tuần đầu tiên.
  2. Đạt $\ge 90\%$ phân loại đúng trên tập kiểm thử (Test set).
  3. Nhân viên CSKH sửa ít hơn 30% nội dung thư nháp. 
  *(Nếu không chuẩn bị được dữ liệu sạch, tự động chuyển về trạng thái **NOT YET** và chỉ triển khai luồng Rule-based router trước).*