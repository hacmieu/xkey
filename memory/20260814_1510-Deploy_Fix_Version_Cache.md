# Deploy Fix — Version Cache Issue

**Date:** 2026-08-14 15:10  
**Status:** ✅ Fixed

## Problem

Menu hiển thị `20260721` thay vì `20260814` mặc dù đã build và deploy.

## Root cause

`cp -R` không xóa hoàn toàn app cũ trong `~/Library/Input Methods/XKeyIM.app`. macOS hoặc cache input method vẫn giữ bản cũ.

## Fix

- Kill XKeyIM
- `rm -rf ~/Library/"Input Methods"/XKeyIM.app`
- `cp -R` bản mới
- Verify `CFBundleVersion` = `20260814`

## Verification

```text
"CFBundleShortVersionString" => "1.2.24"
"CFBundleVersion" => "20260814"
```

## Skill updated

`xkey-build-deploy` skill đã được cập nhật để luôn xóa app cũ trước khi copy.

## Note

Bạn cần re-select XKey input source để menu hiển thị đúng.