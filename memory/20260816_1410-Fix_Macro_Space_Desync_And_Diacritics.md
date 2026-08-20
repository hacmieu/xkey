# Fix Macro, Trailing Spaces on Lost Focus, and Restored Diacritic Bugs

**Date:** 2026-08-16 14:10

- **Macro Fix:** Changed `Space` handler (`0x31`) macro return value to `!settings.addSpaceAfterMacro` in `XKeyIMController.swift` to prevent unwanted extra spaces after macro substitution.
- **Focus Loss / Trailing Spaces Fix:** Added `engine.reset()`, `composingText = ""`, `currentWordLength = 0`, `markedTextStartLocation = NSNotFound` inside `deactivateServer(_:)` and `commitComposition(_:)` so stale space history does not leak into document when switching focus/apps.
- **Diacritic Restore Fix:** Updated DESYNC detection to reset engine whenever `spaceCount > 0` or buffer is non-empty while `composingText` is empty.
- **Build Version:** Updated `CURRENT_PROJECT_VERSION` to `20260816` in `Version.xcconfig` and recompiled via `build_release.sh`.
