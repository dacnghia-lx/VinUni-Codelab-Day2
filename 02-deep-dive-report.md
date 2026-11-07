# 🏗️ Phase 3 — DEEP-DIVE (Nhóm, 85 min)

## 3.1. Current-State Workflow Mapping (25 min)
### 3.1. Current-State Workflow Mapping

Sơ đồ đầy đủ: [`04-workflow-diagram.png`](04-workflow-diagram.png)

```text
┌─────────────────────┐      ┌─────────────────────┐      ┌─────────────────────┐
│ B1. Tiếp nhận khiếu │  🔄  │ B2. Đọc và phân     │  🔄  │ B3. 🔴 Tra dữ liệu  │
│ nại                 │ ───> │ loại                │ ───> │ ở nhiều hệ thống    │
│ Ai: Khách/tài xế →  │      │ Ai: Nhân viên       │      │ Ai: Nhân viên       │
│ nhân viên           │      │ Thời gian: ~1 phút  │      │ Thời gian: ~5 phút  │
│ Thời gian: ~1 phút  │      │                     │      │                     │
│                     │      │                     │      │                     │
└─────────────────────┘      └─────────────────────┘      └─────────────────────┘
                                                                     │
                                                                     │ 🔄 dữ liệu → soạn trả lời
                                                                     ▼
                             ┌─────────────────────┐      ┌─────────────────────┐
                             │ B5. Gửi và cập nhật │  🔄  │ B4. Soạn trả lời,   │
                             │ tình trạng          │ <─── │ quyết định hoàn     │
                             │ Ai: Nhân viên →     │      │ tiền/phạt           │
                             │ khách/tài xế        │      │ Ai: Nhân viên +     │
                             │ Thời gian: ~1 phút  │      │ trưởng nhóm         │
                             │                     │      │ Thời gian: ~2 phút  │
                             └─────────────────────┘      └─────────────────────┘
```

🔴 Bottleneck (B3: dữ liệu nằm rải rác ở nhiều hệ thống) · 🔄 Handoff (4 điểm: khách→nhân viên, nhân viên→các hệ thống, dữ liệu→soạn trả lời, trưởng nhóm duyệt→gửi khách)

⏱ **Tổng = ~10 phút/lượt** (khoảng 8-12 phút; riêng B3 chiếm khoảng một nửa) 

## 3.2. Problem Statement (6-field) & Metrics (15 min)
Điền đầy đủ 6 trường thông tin của bài toán:

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Nhân viên CSKH Xanh SM xử lý yêu cầu của khách và tài xế. Người duyệt xử lý các ca hoàn tiền và xử phạt |
| **2. Current Workflow** | 1. Khách hoặc tài xế gửi khiếu nại qua ứng dụng, đường dây nóng hoặc nhắn tin. 2. Nhân viên đọc và phân loại (sai cước, đồ thất lạc, hủy cuốc, thưởng phạt). 3. Nhân viên tra lịch sử chuyến đi, vị trí di chuyển và thanh toán trên nhiều hệ thống riêng lẻ. 4. Nhân viên soạn câu trả lời theo mẫu có sẵn và quyết định hoàn tiền hoặc xử phạt. 5. Gửi câu trả lời và cập nhật tình trạng xử lý. |
| **3. Bottleneck** | Bước 3: dữ liệu nằm rải rác ở nhiều hệ thống nên tra cứu mất khoảng 4-6 phút trong tổng 8-12 phút cho mỗi khiếu nại. Việc đọc hiểu nội dung khiếu nại và soạn câu trả lời bằng tay cũng tốn nhiều công. |
| **4. Business Impact** | Khoảng 1-2% số chuyến đi phát sinh khiếu nại, trong đó khoảng 50-60% là loại lặp lại (đồ thất lạc, tra cước, hoàn tiền đơn giản). Mỗi khiếu nại tốn 8-12 phút làm việc của nhân viên. Phản hồi chậm khiến khách và tài xế dễ bỏ ứng dụng. Với hàng trăm nghìn chuyến mỗi ngày, khối lượng này tương đương công việc của hàng chục đến hơn 100 nhân viên. |
| **5. Success Metric** | Thời gian xử lý mỗi khiếu nại giảm từ 10 phút xuống dưới 3 phút. Phản hồi đầu tiên cho khách trong dưới 30 phút |
| **6. Operational Boundary** | Được phép: phân loại và sắp xếp mức ưu tiên; lấy dữ liệu của đúng chuyến đi được nêu trong khiếu nại; soạn bản nháp trả lời kèm dẫn chứng. Tuyệt đối không: tự gửi câu trả lời cho khách; tự quyết định hoàn tiền hoặc xử phạt tài xế; xem dữ liệu của chuyến đi không liên quan. Cần người duyệt: mọi trường hợp hoàn tiền và xử phạt |

## 3.3. Future-State Flow & AI Fit

**AI-Fit được chọn:** [ ] Rule / State-Machine · [x] **LLM Feature** · [ ] Agentic Loop

Lý do: phần khó là đọc hiểu lời khiếu nại và viết câu trả lời (LLM làm tốt). Phần nguy hiểm là quyết định hoàn tiền, xử phạt nên giữ cho người làm. AI chỉ soạn bản nháp, không tự gửi. Chưa cần Agentic Loop vì AI chưa phải tự làm nhiều bước liên tiếp mà không có người xem.

### Future-State Flow

```text
┌───────────────────┐    ┌───────────────────┐    ┌───────────────────┐    ┌───────────────────┐
│ F1. Khách/tài xế  │    │ F2. 🔵 AI đọc và  │    │ F3. 🔵 AI tra dữ  │    │ F4. 🔵 AI soạn    │
│ gửi khiếu nại     │ ──>│ phân loại (sai    │ ──>│ liệu đúng chuyến  │ ──>│ bản nháp, gắn     │
│ (app, gọi, nhắn   │    │ cước, thất lạc,   │    │ (chuyến, vị trí,  │    │ nhãn [DRAFT_ONLY] │
│ tin)              │    │ hủy...)           │    │ thanh toán)       │    │                   │
└───────────────────┘    └───────────────────┘    └───────────────────┘    └───────────────────┘
                                   │                                                 │
                                   │ có dấu hiệu nguy hiểm                           │ AI ổn
                                   ▼                                                 ▼
                         ┌───────────────────┐                             ┌───────────────────┐
                         │ 🔴 CHUYỂN NGƯỜI   │                             │ F5. 🟢 Nhân viên  │
                         │ NGAY: an toàn,    │                             │ xem, sửa, duyệt   │
                         │ tai nạn, pháp lý. │                             │ (hoàn tiền/phạt:  │
                         │ Nhân viên xử lý   │                             │ trưởng nhóm       │
                         │ và gọi lại khách  │                             │ duyệt) ~2 phút    │
                         │                   │                             │                   │
                         └───────────────────┘                             └───────────────────┘
                                                                                     │
                                                                                     ▼
                                                                           ┌───────────────────┐
                                                                           │ F6. Nhân viên bấm │
                                                                           │ gửi cho khách/tài │
                                                                           │ xế, cập nhật tình │
                                                                           │ trạng             │
                                                                           └───────────────────┘

↩️ DỰ PHÒNG (bất kỳ điều nào dưới đây → quay về cách làm thủ công cũ B2-B5):
  • AI trả kết quả lỗi, hoặc không chắc chắn về loại khiếu nại → nhân viên tự xử lý
  • Không lấy được dữ liệu chuyến đi, hoặc hệ thống chạy quá 10 giây → nhân viên tự tra
  • Bản nháp thiếu dẫn chứng hoặc lộ thông tin cá nhân → hủy bản nháp, nhân viên soạn lại
  • Khiếu nại về an toàn/tai nạn/pháp lý → chuyển người ngay, AI không soạn
```

🔵 AI step · 🟢 Human step (người duyệt) · ↩️ Dự phòng

**Thời gian dự kiến:** AI làm ~50 giây + người duyệt ~2 phút = **~3 phút/lượt** (so với ~10 phút hiện nay).

---
# 🏁 Phase 5 — EVALUATE (Nhóm, 20 min)

### AI Readiness Checklist:
1. [ ] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test? Có (Đã có sẵn dữ liệu ticket, lịch sử chuyến đi, vị trí GPS và thanh toán trên hệ thống)
2. [ ] Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)? Có (AI chỉ soạn bản nháp [DRAFT_ONLY], nhân viên/trưởng nhóm kiểm tra và bấm gửi; các ca lỗi/tai nạn được chuyển người xử lý ngay)
3. [ ] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ? Có (Đội ngũ CSKH sẵn sàng chuyển sang vai trò kiểm duyệt để giảm thời gian xử lý từ 10 phút xuống dưới 3 phút)

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
[X] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp.

**Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):**
> Dự án hoàn toàn khả thi về mặt kỹ thuật nhờ áp dụng mô hình LLM hỗ trợ phân loại ticket và tự động tổng hợp dữ liệu chuyến đi, đồng thời đảm bảo độ an toàn cao thông qua cơ chế Human-in-the-loop (AI chỉ đóng vai trò tạo bản nháp [DRAFT_ONLY], việc phê duyệt và gửi phản hồi cuối cùng vẫn do nhân viên hoặc trưởng nhóm thực hiện) đi kèm quy trình dự phòng (Fallback) rõ ràng cho các ca lỗi hoặc ca phức tạp liên quan đến an toàn và pháp lý. Về mặt vận hành và chi phí, giải pháp giải quyết trực tiếp điểm nghẽn lớn nhất ở bước tra cứu thông tin, giúp rút ngắn thời gian xử lý khiếu nại từ 10 phút xuống dưới 3 phút và tối ưu hiệu suất cho đội ngũ CSKH khi 50–60% lượng khiếu nại hàng ngày là các trường hợp đơn giản, lặp lại; việc bắt đầu với một Prototype scope hẹp sẽ tối thiểu hóa rủi ro đầu tư mà vẫn mang lại hiệu quả đo lường được ngay

---
