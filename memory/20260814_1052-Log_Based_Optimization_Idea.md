# Log-Based Optimization — XKeyIM

**Date:** 2026-08-14 10:52  
**Status:** 💡 Khả thi — đề xuất (chưa thực thi)

## Trả lời: Có thể 1 ngày đọc log XKey rồi tối ưu không?

**CÓ, hoàn toàn khả thi.** Log đã có cấu trúc máy đọc được, đủ dữ liệu để:
1. **Phát hiện lỗi gõ** (CURSOR MOVED false-positive, engine reset thừa)
2. **Đo hiệu năng** (TIMING call latency)
3. **Tối ưu luồng gõ** (tần suất category, pattern)

## Cấu trúc log hiện tại

Log ghi tại `~/XKey_Debug.log`. Mỗi dòng: `[HH:mm:ss] [XKeyIM] [CATEGORY] message`

### Category (đã thống kê từ log thực tế)

| Category | Số dòng | Ý nghĩa | Dùng cho tối ưu |
|----------|--------:|---------|-----------------|
| `[ENGINE]` | 650 | processKey + engine state | Phát hiện reset thừa, pattern gõ |
| `[CURSOR]` | 641 | Cursor tracking check | Bắt false-positive "CURSOR MOVED" |
| `[OVERLAY]` | 515 | App detection, marked text mode | Tối ưu app classification |
| `[BACKSPACE]` | 453 | Xử lý backspace/restore | Tối ưu xử lý xóa |
| `[TIMING]` | 188 | Latency handleResult | **Đo hiệu năng chính** |
| `[PREWARM]` | 5 | Prewarm engine | - |
| `[ACTIVATE]` | 5 | Activation | - |
| `[SETTINGS]` | 4 | Settings reload | - |
| `[ENTER]` | 4 | Enter viền | - |
| `[MACRO]` | 1 | Macro replacement | - |

## Điểm yếu hiện tại cho tối ưu

1. **`[TIMING]` chưa ghi ms** — chỉ `Calling...`/`completed`, không có elapsed time. Cần thêm duration để đo latency thật.
2. **`[ENGINE]` nhiều log (650)** — cần lọc mức: debug đầy đủ khi phát triển, gọn khi chạy thực tế.
3. **`[OVERLAY]` log mỗi key** — lặp `Client:...` liên tục, gây noise.

## Đề xuất

### Ngắn hạn (1 script, chạy bằng tay)
Viết script phân tích log hiện tại:
- Đếm CURSOR MOVED → tỉ lệ false-positive (reset engine thừa = lỗi gõ)
- Đếm ENGINE reset (`startNewSession`/`resetWithCursorMoved` không có input mới)
- Group theo category/time → tìm hot spot

### Trung hạn (cải thiện log để đo được)
- Thêm **duration (ms)** vào `[TIMING]` logs
- Thêm **flag-level** cho `[ENGINE]` (chỉ ghi khi có sự kiện quan trọng)
- Gộp `[OVERLAY]` log lần đầu mỗi app, không log mỗi key

### Dài hạn (tối ưu code dựa trên data)
- Metric: time-to-first-char, per-keystroke latency
- Tự động dò pattern reset thừa → hint code để fix
- Baseline A/B sau mỗi thay đổi

## Next steps

- [ ] Khảo sát log đầy đủ, tạo script analyzer (`Tools/log_analyzer.swift`)
- [ ] Thêm `duration` vào `[TIMING]` log
- [ ] Xác định baseline hiệu năng

## Files liên quan

- `Shared/DebugLogger.swift` — ghi log tới `~/XKey_Debug.log`
- `XKeyIM/IMKitDebugger.swift` — wrapper cho XKeyIM
- `XKeyIM/XKeyIMController.swift` — nguồn của các category