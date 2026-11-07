# 🔍 Phase 1 — SCAN (Cá nhân, 20 min)

### 📝 List bài toán của tôi:
| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | Vinfast | Lặp lại |Nhân viên tài chính/vận hành đối chiếu thủ công dữ liệu trạm sạc, app, thanh toán và gói miễn phí/ưu đãi, nhiều trường hợp lệch phải điều tra từng ca. |
| 2 | Xanh SM|Lặp lại |CSKH đọc ticket, tra lịch sử chuyến/GPS/thanh toán rồi soạn phản hồi thủ công, trả lời theo mẫu rập khuôn. Ước tính: ~1-2% chuyến phát sinh ticket, mỗi ticket mất 8-12 phút; ~50-60% là loại lặp lại (đồ thất lạc, hoàn tiền, tra cước) |
| 3 |Vinhomes | Lặp lại |Ban quản lý đọc, phân loại, soạn phản hồi khiếu nại và đánh giá 1-star thủ công (15-25 phút/ticket). |
| 4 | Vinhomes|AI-upgrade |Tổng đài/app phản hồi rập khuôn, chậm (>24h); ~30-40% câu hỏi lặp lại có thể tự động hóa. |
| 5 |Vinmec |Lặp lại |Nhập liệu và mã hóa hồ sơ bệnh án (ICD) lặp lại mỗi ca khám. |
| 6 | Vinpearl / VinWonders|AI-upgrade |Chatbot CSKH trả lời rập khuôn, không xử lý được yêu cầu phức tạp (đổi ngày, combo, trẻ em); ~40-50% chat phải chuyển sang người. |
| 7 |ViVinpearl / VinWondersnmec |Stakeholder Pain |Khách chờ lâu ở trò chơi giờ cao điểm; nhân viên thực địa thiếu dự báo luồng khách |

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn **top 3 bài toán** từ danh sách trên: #2 (Xanh SM phân loại/tra cứu dữ liệu), #3 (Vinhomes CSKH), #6 (Vinpearl / VinWonders CSKH)

```
┌────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2 (Xanh SM: phân loại/tra cứu dữ liệu)     │
│                                                                │
│ Bài toán (1 câu): Tự động phân loại, tra cứu dữ liệu chuyến và │
│    soạn nháp phản hồi khiếu nại của khách/tài xế Xanh SM (sai  │
│    cước, đồ thất lạc, hủy cuốc, thưởng/phạt).                  │
│ Công ty thành viên: [x] Xanh SM                                │
│                                                                │
│ Ai đang đau (Actor)? Nhân viên CSKH Xanh SM                    │
│                                                                │
│ Workflow thủ công hiện tại (3-5 bước):                         │
│   1. Khách/tài xế gửi ticket qua app/hotline/chat              │
│   2. CSKH đọc và phân loại (cước, đồ thất lạc, hủy,            │
│      thưởng/phạt)                                              │
│   3. Tra lịch sử chuyến, GPS, thanh toán trên nhiều hệ thống   │
│   4. Soạn phản hồi theo mẫu, quyết định hoàn tiền/xử phạt      │
│   5. Gửi phản hồi và cập nhật trạng thái ticket                │
│                                                                │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3, tra cứu dữ liệu trên  │
│    nhiều hệ thống (~4-6 phút/ticket trong tổng 8-12 phút)      │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 (phân loại, ưu    │
│    tiên); Bước 3 (tự động lấy dữ liệu chuyến liên quan); Bước  │
│    4 (soạn nháp có dẫn chứng, người duyệt với ca hoàn tiền/xử  │
│    phạt)                                                       │
│                                                                │
│ Đo thành công bằng gì (Metric có số)? Thời gian xử lý từ 10    │
│    phút ──> dưới 3 phút/ticket                                 │
│                                                                │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent    │
└────────────────────────────────────────────────────────────────┘
```

```
┌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┐
╎ QUICK PROBLEM CARD #3 (Vinhomes CSKH)                          ╎
╎                                                                ╎
╎ Bài toán (1 câu): Tự động phân loại và soạn nháp phản hồi      ╎
╎    khiếu nại/đánh giá 1-star của cư dân Vinhomes.              ╎
╎ Công ty thành viên: [x] Vinhomes                               ╎
╎                                                                ╎
╎ Ai đang đau (Actor)? Ban quản lý tòa nhà/tổng đài              ╎
╎                                                                ╎
╎ Workflow thủ công hiện tại (3-5 bước):                         ╎
╎   1. Cư dân gửi yêu cầu qua app/hotline/review                 ╎
╎   2. Nhân viên đọc và phân loại (sửa chữa, phí, tiện ích)      ╎
╎   3. Tra cứu quy định và lịch sử căn hộ                        ╎
╎   4. Soạn phản hồi thủ công                                    ╎
╎   5. Chuyển bộ phận xử lý/duyệt và gửi lại                     ╎
╎                                                                ╎
╎ Bước nào tốn thời gian/lỗi nhất? Bước 3 + 4 (~10-15 phút/lượt) ╎
╎ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 (phân loại, ưu    ╎
╎    tiên); Bước 3 (RAG tra cứu quy định); Bước 4 (soạn nháp,    ╎
╎    người duyệt)                                                ╎
╎                                                                ╎
╎ Đo thành công bằng gì (Metric có số)? Thời gian xử lý từ 20    ╎
╎    phút ──> dưới 5 phút                                        ╎
╎                                                                ╎
╎ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent    ╎
└╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┘
```
```
┌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┐
╎ QUICK PROBLEM CARD #6 (Vinpearl / VinWonders CSKH)             ╎
╎                                                                ╎
╎ Bài toán (1 câu): Tự động trích xuất thông tin booking từ      ╎
╎    email/yêu cầu đại lý và đối soát với OTA/PMS thay vì nhập   ╎
╎    tay.                                                        ╎
╎ Công ty thành viên: [x] Khác: Vinpearl / VinWonders            ╎
╎                                                                ╎
╎ Ai đang đau (Actor)? Nhân viên tại các resort và khách sạn     ╎
╎                                                                ╎
╎ Workflow thủ công hiện tại (3-5 bước):                         ╎
╎   1. Nhận email/hotline/file booking từ OTA và đại lý          ╎
╎   2. Đọc và gõ lại thông tin khách, loại phòng, ngày, yêu cầu  ╎
╎      đặc biệt vào PMS                                          ╎
╎   3. Kiểm tra phòng trống, trùng booking, giá/chính sách       ╎
╎   4. Gửi email xác nhận cho khách/đại lý                       ╎
╎   5. Cuối ngày đối soát báo cáo OTA với PMS, sửa lệch          ╎
╎                                                                ╎
╎ Bước nào tốn thời gian/lỗi nhất? Bước 2 (nhập tay) và bước 5   ╎
╎    (đối soát cuối ngày) (~4-6 phút/booking; đối soát ~1-2      ╎
╎    giờ/ngày/cơ sở)                                             ╎
╎ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, bước 5           ╎
╎                                                                ╎
╎ Đo thành công bằng gì (Metric có số)? Thời gian nhập booking   ╎
╎    từ 5 phút ──> dưới 1 phút; sai sót nhập liệu từ ~4% ──>     ╎
╎    dưới 1%                                                     ╎
╎                                                                ╎
╎ Quick Architecture: [x] LLM trích xuất  [x] Rule đối soát      ╎
└╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┘
```