# Fix "The application 'XKey' can't be opened" Error

**Date:** 2026-08-16 15:58

- **Cause:** `open xkey://settings` and opening `/Applications/XKey.app` failed with POSIX Error 162 (`SIGKILL Code Signature Invalid` / `Taskgated Invalid Signature`). The embedded `Sparkle.framework` inside `XKey.app` lacked deep code signing, causing macOS `taskgated` to kill `XKey.app` on launch.
- **Fix:** Added `--deep` flag to ad-hoc `codesign` in `build_release.sh` and re-signed `/Applications/XKey.app`. Verified `codesign -vvv --deep --strict /Applications/XKey.app` is `valid on disk`.
- **Result:** `XKey Settings` opens properly without any error dialogs.
