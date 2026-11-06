# 02 — Deep-Dive Report: Vinmec Patient Intake Assistant

> **Bài toán được chọn:** Card #1 trong `01-problem-scan.md` — *Phân loại chuyên khoa & sàng lọc dấu hiệu cấp cứu khi bệnh nhân đặt lịch khám qua hotline/chat tại Vinmec.*
> **Nội dung:** Phase 3 (DEEP-DIVE) + Phase 5 (EVALUATE) từ `01-worksheet.md`.
> **Lưu ý về số liệu:** Mọi con số khối lượng, thời gian, tỉ lệ là **giả định có cơ sở** để scoping trong lab, được đánh dấu *(giả định)*. Bước đầu tiên khi triển khai là đo baseline thật từ log tổng đài/CRM.

---

# 🏗️ Phase 3 — DEEP-DIVE

## 3.1. Current-State Workflow Mapping

Sơ đồ đầy đủ: **[`04-workflow-diagram.png`](04-workflow-diagram.png)**

```text
┌────────────────┐  🔄   ┌────────────────┐  🔄   ┌────────────────┐
│ B1. Tiếp nhận  │ ────→ │ B2. Hỏi & ghi  │ ────→ │ B3. Chờ điều   │
│ hotline / chat │       │ triệu chứng    │       │ dưỡng tư vấn   │
│ Ai: Tổng đài   │       │ vào CRM 🔴     │       │ (~45% ca) 🔴   │
│ ⏱ 3 phút       │       │ Ai: Tổng đài   │       │ Ai: TĐV→ĐD     │
│ (+chờ máy ~5') │       │ ⏱ 6 phút       │       │ ⏱ chờ ~8 phút  │
└────────────────┘       └───────┬────────┘       └───────┬────────┘
                                 │ ca đơn giản            │
                                 │ (~55%)                 ▼
┌────────────────┐  🔄   ┌───────▼────────┐  🔄   ┌────────────────┐
│ B6. Gửi SMS /  │ ←──── │ B5. Tra lịch   │ ←──── │ B4. Sàng lọc,  │
│ Zalo xác nhận  │       │ HIS & đặt slot │       │ chọn chuyên    │
│ Ai: TĐV/SMS GW │       │ Ai: Tổng đài   │       │ khoa, red flag │
│ ⏱ 1 phút       │       │ ⏱ 4 phút       │       │ Ai: Điều dưỡng │
└────────────────┘       └────────────────┘       │ ⏱ 3 phút       │
                                                  └───────┬────────┘
                                                          ▼ (nếu red flag)
                                                  Gọi lại, hướng dẫn 115 / Cấp cứu
🔴 Bottleneck   🔄 Handoff (4 điểm: lời nói→CRM, CSKH→ĐD, ĐD→CSKH, HIS→SMS)
⏱ Tổng = 25 phút/lượt (ca cần điều dưỡng) · 14 phút/lượt (ca đơn giản)
```

| Bước | Ai thực hiện | Input → Output | Công cụ | Thời gian | Ghi chú |
|---|---|---|---|---:|---|
| B1. Tiếp nhận | Tổng đài viên | Cuộc gọi/chat → yêu cầu mở | Tổng đài, fanpage, app | 3' (+~5' chờ máy giờ cao điểm) | |
| B2. Hỏi & ghi triệu chứng | Tổng đài viên | Lời kể → ghi chú text tự do | CRM | 6' | 🔴 Ghi thiếu khởi phát, mức độ, tuổi; không có chuyên môn y |
| B3. Chuyển điều dưỡng | Tổng đài viên → Điều dưỡng | Ghi chú → yêu cầu tư vấn | Chat nội bộ / điện thoại | chờ ~8' | 🔴 🔄 ~45% ca *(giả định)*; BN giữ máy hoặc được gọi lại |
| B4. Sàng lọc | Điều dưỡng sàng lọc | Ghi chú → chuyên khoa / cờ cấp cứu | Kinh nghiệm cá nhân | 3' | Red flag phụ thuộc người — ca không được chuyển ở B2 có thể bị bỏ sót |
| B5. Đặt lịch | Tổng đài viên | Chuyên khoa → lịch hẹn | HIS | 4' | 🔄 |
| B6. Xác nhận | Tổng đài viên / SMS gateway | Lịch hẹn → SMS/Zalo | SMS/Zalo OA | 1' | 🔄 |
| **Tổng** | | | | **25'** | B2+B3 = 14' ≈ **56%** thời gian |

---

## 3.2. Problem Statement (6-field) & Metrics

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | **Tổng đài viên CSKH** của Vinmec (người thao tác hằng ngày, không có chuyên môn y khoa) và **điều dưỡng sàng lọc** (người được gọi hỗ trợ ca khó). Người chịu hậu quả cuối: **bệnh nhân/người nhà** đặt lịch qua hotline, fanpage, app MyVinmec. |
| **2. Current Workflow** | 6 bước thủ công (xem 3.1): tiếp nhận → hỏi & gõ tay triệu chứng vào CRM → (ca khó) chuyển điều dưỡng qua chat nội bộ/điện thoại → điều dưỡng chọn chuyên khoa và kiểm tra dấu hiệu cấp cứu theo kinh nghiệm → tra lịch bác sĩ trên HIS, đặt slot → gửi SMS/Zalo xác nhận. 4 điểm handoff, **25 phút/lượt** với ca cần điều dưỡng, 14 phút với ca đơn giản. |
| **3. Bottleneck** | **B2 + B3 (14/25 phút):** chuyển mô tả triệu chứng tự do (tiếng Việt đời thường, nhiều triệu chứng lẫn lộn, hay nói giảm "chắc chỉ mệt thôi") thành thông tin có cấu trúc và chuyên khoa đúng. Đây đúng là bước *xử lý ngôn ngữ tự nhiên*. Rủi ro an toàn nằm cùng chỗ: sàng lọc dấu hiệu cấp cứu (red flag) không có checklist chuẩn ở tuyến tổng đài. |
| **4. Business Impact** *(giả định cho 1 bệnh viện, cần đo baseline)* | • ~**400 yêu cầu/ngày** × 14' thời gian thao tác của tổng đài viên ≈ **93 giờ công/ngày (~12 FTE)**.<br>• ~45% ca cần điều dưỡng ≈ **180 lần gián đoạn/ngày** cho đội điều dưỡng.<br>• ~**12% bệnh nhân đến sai khoa** (~48 người/ngày), mỗi ca mất thêm 30–45' chuyển khoa tại viện, tăng tải quầy lễ tân.<br>• ~**15% bỏ cuộc** khi chờ máy giờ cao điểm → mất lượt khám.<br>• **Rủi ro an toàn & uy tín:** một ca đau ngực/đột quỵ bị đặt lịch khám thường thay vì cấp cứu là sự cố nghiêm trọng, không đo bằng tiền được. |
| **5. Success Metric** | **Hiệu suất:** thời gian từ khi BN mô tả xong → có chuyên khoa + slot đề xuất: **25' → < 5'**; thời gian thao tác của tổng đài viên: **14' → ≤ 3'/lượt**.<br>**Chất lượng:** gợi ý chuyên khoa khớp với nhãn điều dưỡng **≥ 90%** trên bộ 500 hồ sơ; tỉ lệ đến sai khoa **12% → < 5%**.<br>**An toàn (điều kiện bắt buộc, không đánh đổi):** recall red flag **= 100%** trên bộ test ≥ 200 ca cấp cứu (gồm ca nói giảm, ca mô tả gián tiếp); escalate nhầm (false positive) **≤ 10%**; **0** phản hồi thiếu thẻ `[NOT_A_DIAGNOSIS]`; **0** phản hồi chứa liều thuốc. |
| **6. Operational Boundary** | **AI ĐƯỢC PHÉP:** hỏi thêm triệu chứng theo khung chuẩn; trích xuất triệu chứng có cấu trúc; gợi ý chuyên khoa; soạn **bản nháp** lịch hẹn (`draft_booking`); kích hoạt escalate cấp cứu.<br>**AI TUYỆT ĐỐI KHÔNG:** chẩn đoán bệnh ("bạn bị X"); nêu tên thuốc, liều lượng, phác đồ; đặt lịch khám thường khi có red flag; khuyên "chờ thêm/theo dõi ở nhà" khi có red flag; bỏ thẻ `[NOT_A_DIAGNOSIS]` dù người dùng tự nhận là bác sĩ/nhân viên; truy cập bệnh án ngoài thông tin tiếp nhận; lưu dữ liệu sức khỏe ra ngoài hệ thống được phép (tuân thủ Nghị định 13/2023/NĐ-CP về bảo vệ dữ liệu cá nhân — dữ liệu sức khỏe là dữ liệu nhạy cảm).<br>**ĐIỂM CẦN NGƯỜI DUYỆT (HITL):** mọi lịch hẹn do tổng đài viên xác nhận trước khi chốt; mọi ca escalate được điều dưỡng gọi lại trong ≤ 2'; ca AI không chắc → chuyển điều dưỡng. |

---

## 3.3. Future-State Flow & AI Fit

### So sánh mức AI Fit (Rule vs LLM vs Agent)

| Tiêu chí | Rule / State-Machine | **LLM Feature** | Agentic Loop |
|---|---|---|---|
| Hiểu mô tả tự do, tiếng Việt đời thường, nói giảm | ❌ Kém — từ khóa bỏ sót cách nói gián tiếp ("thắt ngực", "người cứ vã ra") | ✅ Tốt | ✅ Tốt |
| Tính quyết định, kiểm toán được | ✅ Rất cao | ⚠️ Trung bình (cần guardrail) | ❌ Thấp — nhiều bước tự quyết |
| Phát hiện red flag rõ ràng | ✅ Rất tốt với từ khóa chuẩn | ✅ Tốt cả ca gián tiếp | ✅ |
| Rủi ro hành động sai | Thấp | Thấp nếu chỉ *draft* + HITL | **Cao** — tự đặt lịch/tự gửi tin trong môi trường y tế |
| Chi phí xây dựng & vận hành | Thấp | Thấp–Trung bình | Cao |
| Kết luận | Dùng làm **lưới an toàn** | **Chọn làm lõi** | **Không chọn** — quy trình cố định, không cần tự lập kế hoạch; rủi ro không tương xứng |

**AI-Fit được chọn:** [x] Rule / State-Machine (lớp an toàn) **+** [x] **LLM Feature** (lõi) · [ ] Agentic Loop

Lý do: phần khó là **hiểu ngôn ngữ** (LLM làm tốt), còn phần nguy hiểm là **bỏ sót cấp cứu** (cần lớp rule quyết định, không phụ thuộc xác suất). Hai lớp kiểm tra chéo nhau; bên nào phát hiện red flag thì escalate (**"rule wins"** — chọn phía an toàn).

### Future-State Flow

```text
┌────────────────┐   ┌────────────────┐   ┌────────────────┐   ┌────────────────┐
│ F1. BN mô tả   │   │ F2. 🔵 Rule:   │   │ F3. 🔵 LLM:    │   │ F4. 🔵 Guard-  │
│ triệu chứng    │──→│ quét từ khóa   │──→│ trích xuất     │──→│ rail kiểm tra  │
│ (chat/app hoặc │   │ red flag tiếng │   │ triệu chứng,   │   │ output: thẻ,   │
│ TĐV gõ khi gọi)│   │ Việt (< 1s)    │   │ gợi ý khoa,    │   │ JSON hợp lệ,   │
│ ⏱ ~3'          │   │                │   │ JSON (< 10s)   │   │ không liều     │
└────────────────┘   └───────┬────────┘   └────────────────┘   └───────┬────────┘
                             │ có red flag                             │ OK
                             ▼                                         ▼
                ┌──────────────────────────┐            ┌──────────────────────────┐
                │ 🔴 ESCALATE ngay          │            │ F5. 🟢 HITL: Tổng đài    │
                │ escalate_emergency JSON  │            │ viên xem draft_booking,  │
                │ + hướng dẫn 115/Cấp cứu  │            │ chỉnh nếu cần, chọn slot │
                │ 🟢 Điều dưỡng gọi lại≤2' │            │ HIS gợi ý sẵn ⏱ ~1–2'    │
                └──────────────────────────┘            └────────────┬─────────────┘
                                                                     ▼
                                                        ┌──────────────────────────┐
                                                        │ F6. Gửi SMS/Zalo tự động │
                                                        │ sau khi TĐV bấm duyệt    │
                                                        └──────────────────────────┘
↩️ FALLBACK (bất kỳ điều kiện nào dưới đây → quay về quy trình thủ công cũ B2–B6):
   • Guardrail F4 fail (thiếu thẻ, JSON lỗi, phát hiện liều thuốc) → huỷ output, chuyển TĐV kèm transcript
   • LLM trả specialty "unknown"/không chắc, hoặc BN hỏi lại > 2 lần → chuyển điều dưỡng
   • API lỗi/timeout > 10s → form có cấu trúc + TĐV xử lý như hiện tại
   • Rule báo red flag nhưng LLM không → vẫn ESCALATE (rule wins)
```

🔵 AI step · 🟢 Human step (HITL) · ↩️ Fallback

**Thời gian dự kiến:** ~3' BN mô tả + < 15s AI + 1–2' TĐV duyệt ≈ **< 5'/lượt**; thời gian thao tác của TĐV ≈ **2–3'** (so với 14').

---

# 🏁 Phase 5 — EVALUATE

### AI Readiness Checklist

| # | Câu hỏi | Trạng thái | Bằng chứng / việc cần làm |
|---|---|---|---|
| 1 | Có sẵn dữ liệu mẫu/logs sạch để test? | [ ] **Chưa đủ** | Có log CRM và ghi âm tổng đài, nhưng **chưa gắn nhãn** chuyên khoa chuẩn/red flag và **chưa ẩn danh**. Cần: ẩn danh + gắn nhãn 500 hồ sơ thường và ≥ 200 ca cấp cứu (điều dưỡng gắn nhãn, ~2–3 tuần). |
| 2 | Rủi ro khi AI sai có nằm trong tầm kiểm soát (HITL / Fallback)? | [x] **Có** | AI chỉ tạo *draft*, TĐV duyệt mọi lịch hẹn; red flag có 2 lớp (rule + LLM, rule wins); guardrail output huỷ phản hồi vi phạm; fallback về quy trình cũ. Rủi ro còn lại: LLM bỏ sót ca cấp cứu mô tả rất gián tiếp **và** rule cũng không bắt được → giảm bằng bộ test ≥ 200 ca trước khi cho bệnh nhân dùng trực tiếp. |
| 3 | Stakeholders sẵn sàng thay đổi quy trình? | [x] **Một phần** | Tổng đài viên được lợi trực tiếp (giảm thao tác). Điều dưỡng/khối chuyên môn cần **ký duyệt danh sách red flag** và tiêu chí chuyên khoa; pháp chế cần duyệt câu chữ disclaimer và xử lý dữ liệu. |

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future

- [x] **GO (scope hẹp):** Xây dựng prototype dạng **copilot nội bộ cho tổng đài viên, chạy shadow-mode 4 tuần** tại 1 bệnh viện.
- [ ] NOT YET
- [ ] NO-GO

> Riêng **chatbot cho bệnh nhân dùng trực tiếp: NOT YET** — chỉ mở khi đạt các cổng điều kiện bên dưới.

**Justification (lý giải dựa trên bằng chứng kỹ thuật và chi phí):**

> **Vì sao GO:** (1) Bottleneck B2+B3 chiếm ~56% thời gian và đúng là bài toán hiểu ngôn ngữ tự do — loại việc rule-based làm kém mà LLM làm tốt. (2) Chi phí mô hình rất thấp so với chi phí nhân sự: mỗi lượt khoảng 1.500 token vào + 300 token ra; ở 400 lượt/ngày, chi phí API theo bảng giá Gemini Flash hiện hành chỉ ở mức vài trăm USD/năm *(ước tính bậc độ lớn, cần kiểm tra bảng giá khi lập ngân sách)*, trong khi phần thao tác được giảm tương đương nhiều FTE. (3) Rủi ro được khoanh vùng: ở shadow-mode AI **không nói chuyện với bệnh nhân** — chỉ gợi ý cho TĐV, mọi quyết định vẫn do người; red flag có lớp rule quyết định chạy độc lập.
>
> **Vì sao chỉ scope hẹp:** checklist #1 chưa đạt — chưa có bộ dữ liệu gắn nhãn để chứng minh recall red flag 100%. Shadow-mode giải quyết đúng điểm này: mỗi lượt ghi lại cặp (gợi ý AI ↔ quyết định thực của TĐV/điều dưỡng), tạo ra bộ dữ liệu đánh giá mà không đặt bệnh nhân vào rủi ro.
>
> **Cổng điều kiện để mở cho bệnh nhân dùng trực tiếp:** recall red flag = 100% trên ≥ 200 ca cấp cứu đã gắn nhãn; độ khớp chuyên khoa ≥ 90% trên 500 hồ sơ; 0 vi phạm thẻ/liều thuốc trong 4 tuần shadow; khối chuyên môn và pháp chế ký duyệt. **Điều kiện dừng:** chỉ cần 1 ca red flag bị cả rule và LLM bỏ sót trong shadow-mode → dừng, phân tích nguyên nhân, bổ sung rule trước khi tiếp tục.
