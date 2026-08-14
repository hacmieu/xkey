# Log-Based Optimization — XKeyIM

**Date:** 2026-08-14 10:52  
**Status:** 💡 Khả thi (xác nhận từ log thực tế)

## Kết luận
Có thể đọc log XKey hằng ngày rồi tối ưu. Log hiện đã có cấu trúc category, đủ để:
- Phát hiện lỗi (CURSOR MOVED false-positive, engine reset thừa)
- Đo hiệu năng (TIMING)

## Thống kê category (log 2026-08-13)
| Category | Count |
|----------|------:|
| ENGINE | 650 |
| CURSOR | 641 |
| OVERLAY | 515 |
| BACKSPACE | 453 |
| TIMING | 188 |
| PREWARM | 5 |
| ACTIVATE | 5 |
| SETTINGS | 4 |
| ENTER | 4 |
| MACRO | 1 |

## Điểm yếu để tối ưu
1. TIMING chưa ghi ms (chỉ Calling/completed)
2. ENGINE quá nhiều log (650) — cần flag-level
3. OVERLAY log lặp mỗi key — cần gộp

## Chi tiết
Xem thêm: [`memory/20260814_1052-Log_Based_Optimization_Idea.md`](../memory/20260814_1052-Log_Based_Optimization_Idea.md)