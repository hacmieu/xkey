# Punctuation Context Bug Analysis

**Date:** 2026-08-14 14:50  
**Status:** Root cause hypothesis — pending verification

## User symptom

Gõ sau dấu `,` hoặc `.` hay bị lỗi. Cụ thể: ký tự hoặc dấu ở ngay trước từ mới làm cho từ tiếp theo bị xử lý sai.

## Evidence from log

```text
[XKeyIM] [VNEngine] processWordBreak: char='.', isSpace=false, isRestoreTrigger=true, tempDisableKey=false, index=3, cursorMovedSinceReset=false
[XKeyIM] [VNEngine] startNewSession: cleared (prev='', count=0)
[XKeyIM] [VNEngine] resetWithCursorMoved: cursor moved flag set
```

## Root cause hypothesis

1. Khi gõ dấu `.` hoặc `,`, `XKeyIMController` gọi `engine.processWordBreak(character: character)`.
2. `processWordBreak` kết thúc bằng `cursorMovedSinceReset = false` (reset flag).
3. Tuy nhiên, sau khi insert dấu câu, một số app/editor gửi thêm event `selectedRange()` thay đổi → `handle()` nhận diện `actualLocation != expectedLocation` → đặt `cursorMovedSinceReset = true`.
4. Từ tiếp theo bắt đầu với `cursorMovedSinceReset=true` → `processWordBreak` skip restore, skip macro, và có thể skip Vietnamese processing.

## Why it happens after punctuation

- Sau dấu câu, editor thường cập nhật cursor position (ví dụ: auto-spacing, syntax highlighting, suggestion popup).
- `skipNextCursorCheck` chỉ skip 1 event sau `setMarkedText`, nhưng event cursor từ editor có thể đến muộn hơn 1 event.
- `cursorMovedSinceReset` sau đó không được reset lại cho đến word break tiếp theo.

## Proposed fixes

1. **Reset `cursorMovedSinceReset` on first printable key after punctuation** — not only on word break.
2. **Tighten cursor detection after punctuation** — allow larger tolerance for a short window (e.g. 100ms) after punctuation.
3. **Log `cursorMovedSinceReset` state on first `processKey` after punctuation** để xác nhận giả thuyết.

## Test cases

- Type: `Tôi đi. Hôm nay` → verify `Hôm` không bị tắt Vietnamese
- Type: `Ok, mình làm` → verify `mình` không bị tắt
- Type: `test. đây` → verify `đây` không bị tắt

## Related files

- `XKeyIM/XKeyIMController.swift`
- `XKey/Core/Engine/VNEngine.swift`
