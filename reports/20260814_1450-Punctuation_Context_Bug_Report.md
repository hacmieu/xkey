# Punctuation Context Bug Report

**Date:** 2026-08-14 14:50  
**Status:** Analysis complete — fix proposed

## Summary

Sau dấu `,` `.`, từ tiếp theo dễ bị lỗi vì `cursorMovedSinceReset` bị bật bởi event cursor từ editor.

## Evidence

```text
processWordBreak: char='.', isSpace=false, isRestoreTrigger=true, tempDisableKey=false, index=3, cursorMovedSinceReset=false
startNewSession: cleared (prev='', count=0)
resetWithCursorMoved: cursor moved flag set
```

## Root cause

1. Punctuation gọi `processWordBreak` → reset flag.
2. Editor gửi event cursor update sau punctuation.
3. `handle()` nhận diện cursor moved → `cursorMovedSinceReset = true`.
4. Từ mới bắt đầu với flag này → skip restore/macro/Vietnamese processing.

## Proposed fix

- Reset `cursorMovedSinceReset` trên phím printable đầu tiên sau punctuation.
- Tăng tolerance cho cursor detection trong window ngắn sau punctuation.

## Related

- [memory/20260814_1450-Punctuation_Context_Bug_Analysis.md](../memory/20260814_1450-Punctuation_Context_Bug_Analysis.md)
- [plans/20260814_1450-Punctuation_Context_Bug_Fix.md](../plans/20260814_1450-Punctuation_Context_Bug_Fix.md)
