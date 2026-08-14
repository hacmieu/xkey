# Punctuation Context Bug Fix Plan

**Date:** 2026-08-14 14:50  
**Status:** Proposed

## Goal

Fix lỗi gõ sai sau dấu `,` `.` do `cursorMovedSinceReset` bị bật bởi event cursor từ editor.

## Tasks

- [ ] Thêm log `cursorMovedSinceReset` state khi bắt đầu từ mới sau punctuation
- [ ] Reset `cursorMovedSinceReset` trên phím printable đầu tiên sau punctuation
- [ ] Thêm tolerance window sau punctuation cho cursor detection
- [ ] Viết test: gõ sau `.` và `,`
- [ ] Build và deploy

## Success criteria

- `Tôi đi. Hôm nay` → `Hôm` được xử lý đúng
- `Ok, mình làm` → `mình` không bị tắt Vietnamese
- Không regression cursor fix trước đó
