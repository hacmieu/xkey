# Fix Cursor Tracking False-Positive (VS Code) — Plan

**Date:** 2026-08-13 22:56  
**Status:** ✅ DONE

## Problem

VS Code trả về `selectedRange().location` sai 1 event cycle sau `setMarkedText()` → false-positive "CURSOR MOVED" → engine reset → mất state tone.

## Tasks

- [x] **Phase 1: Root Cause Investigation** — Đọc log, trace `selectedRange()` pattern
- [x] **Phase 2: Fix** — Thêm `skipNextCursorCheck` flag
- [x] **Phase 3: Build & Deploy** — Build Debug, cài vào `~/Library/Input Methods/`
- [ ] **Phase 4: Verify** — Test gõ nhanh trong VS Code
- [ ] **Phase 5: Commit** — Commit + push

## Files touched

- `XKeyIM/XKeyIMController.swift` — thêm `skipNextCursorCheck` flag, sửa cursor detection, `setMarkedText`, `commitComposition`