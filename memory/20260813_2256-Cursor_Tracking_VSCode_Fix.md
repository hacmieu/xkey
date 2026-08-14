# Cursor Tracking False-Positive Fix — XKeyIM

**Date:** 2026-08-13 22:56

## Root Cause

VS Code (và một số editor khác) trả về `selectedRange().location` sai **trong đúng 1 event cycle** sau `setMarkedText()`. Cụ thể: `selectedRange().location` = `1` (hoặc giá trị cũ), trong khi vị trí thực của cursor là `100`.

Code cũ detect mỗi lần gõ phím tiếp theo là "CURSOR MOVED" → `engine.resetWithCursorMoved()` → **mất state tone** → bỏ dấu thất bại.

## Evidence từ log

```
[22:47:45] 'd' key: actual=1, expected=100, composing='d', markedStart=99
[22:47:45] CURSOR MOVED (composing): actual=1 != expectedEnd=100 or ±1, markedStart=99
```

→ `actual=1` (VS Code bug), `expectedEnd=100` → cách nhau 99 đơn vị → engine reset → `dd` không thành `đ`.

## Fix

Thêm flag `skipNextCursorCheck` trong `XKeyIMController`:

1. **`setMarkedText()`**: Sau khi gọi `setMarkedText`, set `skipNextCursorCheck = true`
2. **`commitComposition()`**: Sau khi commit, set `skipNextCursorCheck = true`
3. **`handle()`**: Bỏ qua cursor check nếu `skipNextCursorCheck == true`, sau đó reset flag về `false`

## Files changed

- `XKeyIM/XKeyIMController.swift`:
  - Thêm property `skipNextCursorCheck: Bool`
  - Thêm điều kiện `!skipNextCursorCheck` trong cursor detection
  - Set `skipNextCursorCheck = true` trong `setMarkedText()` và `commitComposition()`
  - Reset `skipNextCursorCheck = false` sau mỗi `handle()` call