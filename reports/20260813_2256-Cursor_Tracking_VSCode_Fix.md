# Cursor Tracking False-Positive Fix — XKeyIM

**Date:** 2026-08-13 22:56  
**Status:** ✅ Deployed (build Debug, chưa commit)

## Symptom

Gõ nhanh trong VS Code với XKeyIM → bỏ dấu thất bại. Đặc biệt lỗi ở các ký tự bỏ dấu ở đầu từ (vd: `dd` → `đ`, `heer` → `hẻ`, `cuar` → `của`).

## Root Cause

VS Code trả về `selectedRange().location` sai trong **đúng 1 event cycle** sau `setMarkedText()`. Cụ thể: `selectedRange().location = 1` (giá trị stale), trong khi vị trí thực của cursor là ~100.

Code cũ detect lệch này là "CURSOR MOVED" → `engine.resetWithCursorMoved()` → **mất state tone đang build** → bỏ dấu thất bại.

## Evidence (từ log)

```
[22:47:45] 'd' key: actual=1, expected=100, composing='d', markedStart=99
[22:47:45] CURSOR MOVED (composing): actual=1 != expectedEnd=100 or ±1, markedStart=99
[22:47:45] startNewSession: cleared (prev='d', count=1)  ← engine reset, mất 'd'
[22:47:45] 'd' key thứ 2: được insert vào buffer mới → "dd" thay vì "đ"
```

## Fix

Thêm flag `skipNextCursorCheck` trong `XKeyIMController`:

| Location | Action |
|----------|--------|
| `setMarkedText()` | Set `skipNextCursorCheck = true` sau khi `setMarkedText` |
| `commitComposition()` | Set `skipNextCursorCheck = true` sau khi commit |
| `handle()` cursor check | Skip nếu `skipNextCursorCheck == true` |
| `handle()` cuối block | Reset `skipNextCursorCheck = false` |

## Files changed

- `XKeyIM/XKeyIMController.swift` (~10 dòng thêm)

## Test

Build Debug → deploy `~/Library/Input Methods/XKeyIM.app` → chọn XKey input source → gõ nhanh `thoong` `heer` `cuar` `dd ow` trong VS Code.