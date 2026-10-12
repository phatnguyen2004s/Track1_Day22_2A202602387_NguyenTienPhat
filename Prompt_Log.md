# Prompt Log — Day 22 Monetization (HoaDonAI giả định)

> **Cách đọc file này.** Đề (§4.7) yêu cầu AI chỉ là công cụ phản biện, không phải tác giả: với mỗi điểm AI nêu, bạn quyết định accept / reject / partial và **tự viết lại bằng lời của mình**. Người phản biện ở đây là Claude, trong cùng phiên dựng bài, nên cột "Quyết định đề xuất" chỉ là gợi ý để bạn đối chiếu. Mọi con số bên dưới lấy thẳng từ `NguyenTienPhat_Day22_model.xlsx`. Prompt chạy: **4.7.1, 4.7.2, 4.7.3** (tối thiểu cần 2). Chưa chạy: 4.7.4, 4.7.5, 4.7.6; nên chạy 4.7.5 (One-Pager Defensibility Check) trên One-Pager thật của bạn trước khi nộp.

## 4.7.1 — Cost/Job Stress Test

**Input đã dán:** toàn bộ Tab 1 (S1–S8): 10.000 job thử/tháng, containment 80% (ước tính), Sonnet 5.5, 2 lượt/job, 5.000 token cache + 1.500 fresh + 400 output mỗi lượt, retry 7%, Infra $0,0075/job, QA 4% × 3 phút, Biến thể A.

| # | Điểm phản biện | Phân tích (số từ Excel) | Quyết định đề xuất | Thay đổi ở Excel |
|---|---|---|---|---|
| 1a | Thiếu **phí cổng thanh toán** | Khoản này không nằm trong 5 thành phần của đề; ước tính 2,5% doanh thu làm GM từ 69,5% còn 67,0%. | **Partial** | Không đưa vào Cost/Job; thêm dòng ghi nhớ `2_Pricing!B50` (GM sau phí cổng thanh toán). Tỷ lệ 2,5% là ước tính, chưa xác minh. |
| 1b | **Observability / chạy eval** hay bị quên | Đã có trong Infra (`$0,0020`/job) nhưng đó là giả định, chưa đo; LLM-judge chạy trên mẫu 5% còn tốn thêm token. | **Accept** | Giữ dòng riêng ở S5 để không bị lẫn; ghi nhận cần đo khi có eval. |
| 1c | **Retry/timeout** = 7% chưa đo | Retry cộng thêm $10,84/tháng, đủ nhỏ nhưng là ước tính. | **Accept** | Giữ 7% (trong dải 5–10% của đề), đánh dấu "ước tính". |
| 1d | **Egress / storage / vector DB tăng theo thời gian** | Hai dòng storage + vector chiếm $0,0015/job, tức ~4% Cost/Job; tăng tuyến tính khi lưu 12 tháng. | **Accept** | Không đổi số; nhắc rà lại khi có dữ liệu thật. |
| 1e | **Ai chịu chi phí HITL?** | Biến thể A: Cost/Job $0,0379, GM 69,5%. Biến thể B: Cost/Job $0,1028, GM 17,2% (loại). | **Accept** | Đã chọn A kèm lý do ở `1_Cost_Job` S7; B tính song song ở cột C. |
| 2 | **Denominator check** | Chia cho job thử cho $0,0303; chia cho job hoàn thành cho $0,0379, tức bản sai thấp hơn thực tế 25%. | **Accept** | Đã có dòng minh họa bẫy mẫu số ở `1_Cost_Job` (build-up). |
| 3 | **Token math audit** | Tính lại: cache $0,00148 + fresh $0,00600 + output $0,00800 = **$0,01548**/job; không cache $0,03400; cache tiết kiệm **54,5%**. Batch API −50% → $0,00774/job, Cost/Job $0,0275. Batch khả thi vì job không cần realtime (giao kết quả trong ngày), nhưng cache hit trong Batch chỉ là "best effort". | **Accept** (không tìm thấy lỗi số học) | Batch giữ làm "số thứ hai", không dùng cho base case. |
| 4 | **Price volatility** | Base case chỉ dùng giá chuẩn Anthropic kiểm tra 13/10/2026; không có giá khuyến mại nào. Đổi model: Haiku 5.5 → GM 85,3% (>85% = nghi quên chi phí); Haiku 4.5 → 77,3%; Sonnet 5.5 → 69,5%; Opus 5.5 → 52,8% (< 60%, loại). | **Accept** | Bảng stress model ở `2_Pricing` mục G. Chưa xác nhận lại giá list OpenAI $5/$30 (chỉ thấy giá khuyến mại $4/$20 đến ≥ 21/11/2026). |
| 5 | **Breakeven sensitivity** | Containment tối thiểu để GM ≥ 60%: **57,5%** (Biến thể A), 94,1% (Biến thể B). GM Biến thể A tại 50/60/70/80/90%: 55,0% / 61,4% / 66,0% / 69,5% / 72,2%. Biến thể B tại các mức đó: -154% / -78% / -24% / 17% / 49%. | **Accept** | Nghiệm đóng và bảng sensitivity ở `2_Pricing` mục E–F. |
| 6 | **Con số giết bạn** | Sai 2×: containment giảm một nửa → GM 45,3% (tụt 24,2 điểm); chi phí API ×2 → 52,8%; Infra ×2 → 62,0%; HITL ×2 → 63,2%. | **Accept** | Containment là con số chưa đo và nguy hiểm nhất → Evidence Pack "Eval Results" (deadline 10/11/2026). |

**Điểm yếu khác tôi thấy khi chạy prompt (bạn cần quyết định):** (i) mọi đầu vào ở Tab 1 là giả định, chưa đo; (ii) overhead chỉ được phủ khi có ~25 khách trả phí, trong khi mục tiêu tháng 3 là 15 khách; (iii) Infra $75/tháng cố định chia cho 10.000 job — nếu volume thấp hơn, Infra/job tăng gấp bội.

## 4.7.2 — Value Metric Challenger

**Input đã dán:** job = hóa đơn hoàn thành; người mua = SME Việt Nam; ngân sách Nhân sự/Vận hành; Attribution THẤP (3,2/5), Autonomy THẤP (3,2/5); Cost/Job $0,0379; giá đề xuất $0,1242; lựa chọn của tôi: Hybrid.

| # | Điểm phản biện | Quyết định đề xuất | Ghi chú |
|---|---|---|---|
| 1 | Ma trận gợi ý "Seat / Hybrid"; Hybrid khớp. Không chọn Outcome vì chưa có eval làm bằng chứng Attribution. | **Accept** | `3_Value_Metric`, ô "Kiểm tra khớp ma trận". |
| 2a | Câu hỏi khó nhất của người mua: "Phí nền mua cái gì?" | **Accept** | Phí nền đổi lấy kết nối phần mềm kế toán + SLA trong ngày + hạn mức hóa đơn (`2_Pricing` mục C). |
| 2b | **Khách ít hóa đơn bị thiệt:** với phí nền 1.100.000 đ, khách dưới ~143 hóa đơn hoàn thành/tháng trả đắt hơn thuê người (7.692 đ/hóa đơn). | **Partial** — đây là lỗ hổng thật | Chưa mô hình hóa. Đề xuất: thêm gói nhỏ hơn (ví dụ 50 hóa đơn). **Bạn quyết định và tính lại.** |
| 2c | Tranh chấp: hóa đơn bị kế toán sửa ở ngày thứ 8 vẫn bị tính tiền? | **Partial** | Chưa có điều khoản hoàn tiền; cần viết rõ trước khi gửi khách. |
| 3 | 3 sản phẩm thật: Veryfi (Hybrid: $500/tháng tối thiểu + $0,16/hóa đơn), Dext (gói theo hạn mức, $25,21/tháng cho 250 tài liệu), Intercom Fin (Outcome $0,99/resolution, khác loại job). | **Accept** | Link và ngày kiểm tra ở `3_Value_Metric` và `6_Benchmarks`. Intercom chưa kiểm tra lại. |
| 4 | Hành vi khách làm Hybrid lỗ: upload hàng loạt hóa đơn scan nhiều trang → token/job gấp ~3×. Hybrid giới hạn rủi ro nhờ phần vượt gói, nhưng cần điều khoản fair-use. | **Partial** | Chưa mô hình hóa trong Excel. |

## 4.7.3 — Channel Reality Check

**Input đã dán:** ARPU $64,57/tháng, GM 69,5%, phân khúc SMB, ngân sách CAC $538,54, kênh PLG, Pain Moment ngày 17–20 hàng tháng.

| # | Điểm phản biện | Quyết định đề xuất | Ghi chú |
|---|---|---|---|
| 1 | Một AE phải chốt **2,58 deal/ngày** ở ACV $775 → Sales-Led không khả thi về số học. | **Accept** | `4_Channel_Fit` mục 2. |
| 2 | CAC thực $25.200 so với ngân sách $539 → lệch **46,8 lần**. | **Accept** | `4_Channel_Fit` mục 3. |
| 3 | Pain Moment đủ cụ thể để gọi tên bề mặt tích hợp chưa? Gần đủ, thiếu: hóa đơn điện tử đến từ những nhà cung cấp nào và định dạng email ra sao. | **Partial** | Cần xác minh khi phỏng vấn 10 kế toán ở tháng 1. |
| 4 | Partner-Led: không chọn, nên không áp dụng. | **N/A** | — |
| 5 | Lập luận mạnh nhất CHỐNG PLG: người dùng (kế toán) không phải người quyết định chi tiền (chủ SME), ARPU thấp và nhu cầu dồn vào cuối tháng nên dễ churn. Cách bác bỏ nhanh nhất trong 2 tuần: concierge test với 10 kế toán; dưới 5 người forward hóa đơn thật thì dừng. | **Accept** | Đã thêm tiêu chí dừng ở `5_90Day_Plan` (Tháng 2–3) và `4_Channel_Fit` mục 5. |

## Việc còn lại cho bạn

1. Thay các ô VÀNG bằng số liệu sản phẩm thật (nếu HoaDonAI không phải sản phẩm của bạn) rồi chạy lại 4.7.1–4.7.3 với dữ liệu thật.
2. Viết lại Decision Note, lý do chọn kênh và tiêu chí Evidence Pack bằng lời của bạn.
3. Chạy 4.7.5 trên One-Pager trước khi nộp.
