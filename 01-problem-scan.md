# 01 — Problem Scan & Quick Cards (Vin Smart Future)

> **Người thực hiện:** `dacnghia-lx` (branch cá nhân)
> **Nội dung:** Phase 1 (SCAN) và Phase 2 (QUICK-ASSESS) từ `01-worksheet.md`.
> **Lưu ý về số liệu:** Các con số thời gian, khối lượng và tỉ lệ dưới đây là **ước tính giả định** dựa trên quan sát quy trình phổ biến và brainstorm cùng AI. Chúng là giả thuyết cần xác minh với log vận hành thực tế, không phải số liệu chính thức của Vingroup.

---

# 🔍 Phase 1 — SCAN

Dùng **4 Lenses** (Lặp lại · Tốn thời gian · AI có thể tốt hơn · Pain từ người khác) để quét vận hành của các công ty thành viên. Kết quả: **6 bài toán**, phủ đủ 4 lenses và 5 công ty thành viên.

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Vinmec** | Pain từ người khác | Khi bệnh nhân gọi hotline/chat để đặt lịch, tổng đài viên (không có chuyên môn y khoa) phải đoán chuyên khoa từ mô tả triệu chứng tự do; ca khó phải chờ điều dưỡng sàng lọc tư vấn. Bệnh nhân chờ lâu, ~10–15% đến sai khoa phải chuyển khoa tại viện. Nguy hiểm nhất: ca có dấu hiệu cấp cứu (đau ngực, đột quỵ) vẫn có thể bị đặt lịch khám thường. |
| 2 | **Vinmec** | Tốn thời gian | Bác sĩ nội trú mất 20–30 phút/bệnh nhân để soạn tóm tắt xuất viện (discharge summary) từ bệnh án điện tử, xét nghiệm và ghi chú điều trị. |
| 3 | **VinFast** | AI có thể tốt hơn | Cố vấn dịch vụ tại xưởng ghi nhận mô tả lỗi bằng tiếng Việt tự do của khách ("đi qua gờ giảm tốc kêu cụp cụp bánh trước") rồi tự tra mã lỗi kỹ thuật; ghi nhận thiếu, phân sai nhóm lỗi khiến kỹ thuật viên phải hỏi lại khách. |
| 4 | **Vinhomes** | Lặp lại | Ban quản lý tòa nhà đọc và phân loại thủ công hàng trăm phản ánh/ngày trên App Vinhomes Resident (mất nước, hỏng đèn, ồn, vệ sinh) rồi chuyển đến đúng bộ phận; thời gian phản hồi đầu tiên có thể tới 12 giờ. |
| 5 | **Xanh SM** | Pain từ người khác | Tài xế phàn nàn điểm đón gợi ý không chính xác ở khu đô thị lớn (cổng/sảnh/tòa); khách phải nhắn/gọi mô tả lại vị trí, tài xế mất 3–5 phút/cuốc tìm khách. |
| 6 | **Vinpearl** | Tốn thời gian | Nhân viên sales đọc email đặt phòng theo đoàn (group booking) từ công ty lữ hành — nhiều yêu cầu lẫn trong văn bản tự do — rồi tự kiểm tra quỹ phòng và soạn báo giá, mất 30–45 phút/email. |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Chọn top 3 theo tiêu chí: **(a)** pain lớn và đo được, **(b)** có bước xử lý ngôn ngữ tự nhiên mà rule-based làm kém, **(c)** có thể đặt ranh giới an toàn rõ ràng. → Chọn **#1 (Vinmec), #3 (VinFast), #4 (Vinhomes).**

## Card #1 — Vinmec: Phân loại chuyên khoa & sàng lọc dấu hiệu cấp cứu khi đặt lịch

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Tổng đài viên mất ~14 phút/lượt và phải   │
│ chờ điều dưỡng để xác định đúng chuyên khoa từ mô tả triệu  │
│ chứng tự do, đôi khi bỏ sót dấu hiệu cấp cứu.               │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [x] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Tổng đài viên CSKH (quá tải giờ cao    │
│ điểm), điều dưỡng sàng lọc (bị gọi hỏi liên tục), bệnh nhân │
│ (chờ lâu, đến sai khoa).                                    │
│                                                             │
│ Workflow thủ công hiện tại (6 bước):                        │
│   1. BN gọi hotline/chat mô tả triệu chứng                  │
│   ──> 2. Tổng đài viên hỏi & gõ tay triệu chứng vào CRM     │
│   ──> 3. Ca khó: chuyển điều dưỡng sàng lọc (chờ)           │
│   ──> 4. Điều dưỡng chọn chuyên khoa / phát hiện red flag   │
│   ──> 5. Tổng đài viên tra lịch bác sĩ trên HIS, đặt lịch   │
│   ──> 6. Gửi SMS/Zalo xác nhận                              │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2–3                   │
│   (⏱ 6 phút hỏi/ghi + ~8 phút chờ điều dưỡng = 14 phút)     │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2–4: trích xuất  │
│ triệu chứng có cấu trúc, gợi ý chuyên khoa, cắm cờ red flag │
│ để escalate ngay (không chờ).                               │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   - Thời gian từ lúc BN mô tả → có chuyên khoa + slot:      │
│     25 phút ──> dưới 5 phút                                 │
│   - Tỉ lệ đến sai khoa: ~12% ──> dưới 5%                    │
│   - Recall red flag trên bộ test: 100% (0 ca bị bỏ sót)     │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent │
│   (Rule = lưới an toàn red-flag; LLM = hiểu mô tả tự do)    │
└─────────────────────────────────────────────────────────────┘
```

**Tự phản biện (CFO/Trưởng vận hành khó tính):**
- *"Một cây quyết định (decision tree) trên form là đủ?"* → Đủ cho red flag rõ ràng (đã giữ lại làm lớp Rule), nhưng bệnh nhân mô tả bằng ngôn ngữ đời thường, nhiều triệu chứng lẫn lộn, có người nói giảm ("chắc chỉ mệt thôi") — form cứng làm tỉ lệ bỏ cuộc cao.
- *"Metric 'đến sai khoa 12%' lấy từ đâu?"* → Hiện là giả định; cần đo baseline từ log chuyển khoa tại quầy lễ tân 4 tuần trước khi cam kết.
- *"Rủi ro pháp lý khi AI 'chẩn đoán'?"* → Ranh giới: AI không chẩn đoán, không kê đơn, mọi phản hồi gắn `[NOT_A_DIAGNOSIS]`, red flag luôn escalate.

---

## Card #2 — VinFast: Chuẩn hóa mô tả lỗi xe của khách thành mã lỗi sơ bộ

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Cố vấn dịch vụ mất ~10 phút/xe để chuyển  │
│ mô tả lỗi tiếng Việt tự do của khách thành phiếu sửa chữa   │
│ có nhóm lỗi/mã lỗi sơ bộ, và thường ghi thiếu thông tin.    │
│ Công ty thành viên: [x] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Cố vấn dịch vụ (service advisor) tại   │
│ xưởng; kỹ thuật viên (phải hỏi lại khách); khách (chờ lâu). │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Khách mô tả lỗi tại quầy/qua app                       │
│   ──> 2. Cố vấn ghi chú tay vào DMS                         │
│   ──> 3. Cố vấn tra sổ tay mã lỗi, chọn nhóm lỗi            │
│   ──> 4. Kỹ thuật viên đọc phiếu, gọi hỏi lại nếu thiếu     │
│   ──> 5. Kiểm tra xe, đọc mã OBD, xác nhận lỗi              │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3–4 (⏱ ~10 phút/xe,   │
│   ~30% phiếu phải hỏi lại khách — ước tính)                 │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2–3: trích xuất  │
│ triệu chứng (vị trí, điều kiện xuất hiện, tần suất), gợi ý  │
│ top-3 nhóm lỗi và câu hỏi bổ sung còn thiếu.                │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   - Thời gian lập phiếu: 10 phút ──> dưới 3 phút            │
│   - Phiếu phải hỏi lại khách: ~30% ──> dưới 10%             │
│   - Top-3 nhóm lỗi gợi ý chứa lỗi đúng: ≥ 85%               │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

**Tự phản biện:** Lỗi liên quan an toàn (phanh, lái, pin cao áp) không được để AI "khoanh vùng" sai rồi kỹ thuật viên bỏ qua kiểm tra → AI chỉ gợi ý, chẩn đoán cuối cùng luôn dựa trên kiểm tra thực tế + mã OBD. Cần bộ dữ liệu phiếu sửa chữa cũ đã gán nhãn để đo độ chính xác.

---

## Card #3 — Vinhomes: Phân loại & điều hướng phản ánh cư dân

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Ban quản lý đọc và phân loại thủ công     │
│ ~300 phản ánh/ngày/khu đô thị, thời gian phản hồi đầu tiên  │
│ có lúc tới 12 giờ.                                          │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên CSKH ban quản lý; cư dân     │
│ (chờ phản hồi); kỹ thuật tòa nhà (nhận việc sai bộ phận).   │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Cư dân gửi phản ánh trên App Resident (text + ảnh)     │
│   ──> 2. CSKH đọc, phân loại, xác định mức khẩn             │
│   ──> 3. Chuyển ticket cho bộ phận (kỹ thuật/vệ sinh/an ninh)│
│   ──> 4. Soạn phản hồi xác nhận cho cư dân                  │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 4                 │
│   (⏱ ~5 phút/ticket, dồn hàng đợi ngoài giờ hành chính)     │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 (phân loại +   │
│ mức khẩn) và Bước 4 (draft phản hồi để CSKH duyệt).         │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   - Thời gian phản hồi đầu tiên: 12 giờ ──> dưới 30 phút    │
│   - Phân loại đúng bộ phận: ≥ 90%                           │
│   - Ticket khẩn (rò điện, ngập, cháy) route trong < 2 phút  │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

**Tự phản biện:** Phần lớn ticket có thể route bằng danh mục chọn sẵn trên app (rule-based) — AI chỉ đáng tiền cho ~30–40% ticket mô tả tự do/nhiều vấn đề. Phản hồi liên quan phí quản lý, tranh chấp có rủi ro pháp lý → bắt buộc CSKH duyệt.

---

# 🗳️ Lựa chọn bài toán cho Deep-Dive

**Chọn Card #1 — Vinmec: Phân loại chuyên khoa & sàng lọc dấu hiệu cấp cứu khi đặt lịch.**

| Tiêu chí | Card #1 Vinmec | Card #2 VinFast | Card #3 Vinhomes |
|---|---|---|---|
| Mức độ pain (thời gian + hậu quả) | **Cao** — 25 phút/lượt, có rủi ro bỏ sót ca cấp cứu | Trung bình | Trung bình |
| Rule-based có giải quyết đủ không? | Không — mô tả tự do, nói giảm triệu chứng | Một phần | **Phần lớn có** (danh mục chọn sẵn) |
| Ranh giới an toàn có đặt được rõ ràng? | Có — tag `[NOT_A_DIAGNOSIS]` + escalate red flag + HITL | Có | Có |
| Có thể prototype bằng prompt trong lab? | **Có** (đã làm trong `starter-code/prompt_prototype.py`) | Cần dữ liệu mã lỗi | Có |

**Lý do loại các card khác:**
- **Card #2 (VinFast):** cần bộ sổ tay mã lỗi và phiếu sửa chữa nội bộ để AI gợi ý có ý nghĩa — chưa có trong phạm vi lab; prototype bằng prompt thuần sẽ dễ "bịa" mã lỗi.
- **Card #3 (Vinhomes):** phần lớn giá trị có thể đạt bằng rule-based routing theo danh mục; nên làm rule trước, đo xem phần còn lại có đủ lớn để cần LLM không.
