# Log Analysis 14:15 2026-08-14 — XKeyIM Typing Issues

**Date:** 2026-08-14 14:18  
**Log range:** 14:15–14:19  
**Status:** Analyzed — 4 issues found

## Issues Found

### 1. `skipNextCursorCheck` Over-Skipping
- **Evidence:** `Cursor check: SKIPPED (skipNextCursorCheck=true, actual=1 expected=142)`
- **Impact:** Flag bỏ qua cả những lần cursor off nhiều, không chỉ stale selectedRange() sau `setMarkedText`. Nếu user click chuột hoặc dùng arrow keys, engine vẫn giữ state cũ → desync.
- **Fix direction:** Only skip if the off-by-one happens immediately after setMarkedText/commit, not unconditionally.

### 2. Macro Log Leaks User Content
- **Evidence:** `[MACRO] Space: Macro replacement 'Ghi log vào ./memory/, ./plans/, ./reports/ dạng...'`
- **Impact:** Log file chứa plaintext nội dung macro expansion, có thể là sensitive text.
- **Fix direction:** Log chỉ macro key + length, không log replacement text.

### 3. `handleVowelKey: no pattern matched`
- **Evidence:** `handleVowelKey: no pattern matched, inserting key` xuất hiện khi gõ `O`, `ki`, `tie`
- **Impact:** Một số nguyên âm không được xử lý đúng, dẫn đến phải gõ lại.
- **Fix direction:** Kiểm tra adaptive mode và Telex/VNI routing cho nguyên âm đơn lẻ.

### 4. English Pattern Detection Still Aggressive
- **Evidence:** `ENGLISH PATTERN DETECTED` xuất hiện nhiều lần
- **Impact:** Các từ tiếng Anh xen kẽ bị disable Vietnamese processing, nhưng sau đó khi user tiếp tục gõ tiếng Việt vẫn còn `tempDisableKey=true`.
- **Fix direction:** Reset `tempDisableKey` nhanh hơn khi bắt đầu từ mới; cải thiện English detection cho từ như `adsense`.

## Recommended Next Build Changes

1. Tighten `skipNextCursorCheck` condition
2. Redact macro replacement text in logs
3. Investigate `handleVowelKey` false negatives
4. Improve English detection reset behavior

## Related

- `XKeyIM/XKeyIMController.swift`
- `XKey/Core/Engine/VNEngine.swift`
- `Shared/DebugLogger.swift`