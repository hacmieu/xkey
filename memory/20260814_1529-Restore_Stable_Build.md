# Restore Stable Build v1.2.24-20260721

**Date:** 2026-08-14 15:29  
**Status:** ✅ Restored

## Action

Xóa hẳn các thay đổi chưa commit, checkout về tag `v1.2.24-20260721`, build và deploy.

## Commands run

```bash
git reset --hard HEAD
git checkout v1.2.24-20260721
xcodebuild -project XKey.xcodeproj -scheme XKeyIM -configuration Debug build CODE_SIGN_IDENTITY="" CODE_SIGNING_REQUIRED=NO CODE_SIGNING_ALLOWED=NO
killall XKeyIM
rm -rf ~/Library/"Input Methods"/XKeyIM.app
cp -R /Users/hacmieu/Library/Developer/Xcode/DerivedData/XKey-aivrjtwsdauhfffnuyvnzsykgsnj/Build/Products/Debug/XKeyIM.app ~/Library/"Input Methods"/XKeyIM.app
```

## Verification

```text
"CFBundleShortVersionString" => "1.2.24"
"CFBundleVersion" => "20260721"
```

## Note

Bạn cần re-select XKey input source.
Các thay đổi cursor tracking, macro, punctuation fix đã bị xóa. Nếu muốn retry từng cái một cách có kiểm soát, ta có thể cherry-pick từng commit nhỏ.