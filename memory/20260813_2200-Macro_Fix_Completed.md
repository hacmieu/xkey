# XKeyIM Macro Fix

**Date:** 2026-08-13 22:00

- Mapped Macro Manager to `XKeyIMController` which lacked the data parsing for macros.
- Added `isMacroReplacement` flag to `ProcessResult` to intercept Macro execution vs normal Spell Check restore in `VNEngine.swift`.
- Added logic in `XKeyIMController` key event `0x31` (Space) to replace the composing/tracked word with macro replacements immediately.
- Changed `CURRENT_PROJECT_VERSION` to `20260813` in `Version.xcconfig`.
- Successfully built `XKeyIM` and installed to `~/Library/Input Methods/`.
