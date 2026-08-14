# Punctuation Context Bug — Fix Deployed

**Date:** 2026-08-14 14:55  
**Status:** ✅ Fix implemented & deployed

## Changes

- `XKeyIM/XKeyIMController.swift`:
  - Thêm `lastPunctuationTimestamp: Date` để theo dõi thời điểm vừa gõ dấu câu/symbol.
  - Bỏ qua cursor-move detection trong 50ms sau punctuation để editor cursor updates không reset engine.
  - Cập nhật `lastPunctuationTimestamp` sau khi insert punctuation/symbol.

## Build & Deploy

- Build: `xcodebuild -project XKey.xcodeproj -scheme XKeyIM -configuration Debug build CODE_SIGN_IDENTITY="" CODE_SIGNING_REQUIRED=NO CODE_SIGNING_ALLOWED=NO`
- Status: ✅ BUILD SUCCEEDED
- Deploy: ✅ Đã kill XKeyIM và copy bản mới vào `~/Library/Input Methods/XKeyIM.app`

## Test cases cần verify

- `Tôi đi. Hôm nay` → `Hôm` xử lý đúng
- `Ok, mình làm` → `mình` xử lý đúng
- `test. đây` → `đây` xử lý đúng

## Note

Bạn cần re-select XKey input source sau khi deploy.