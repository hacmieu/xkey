# English Detection Auto-Restore Memory

- **Date**: 2026-08-25
- **Task**: Implemented the fix for the annoying English typing experience.
- **Details**: 
  - Added `isInstantRestore` to `ProcessResult`.
  - Triggered `checkRestoreIfWrongSpelling` with `extCode = 5` in `VNEngine.swift` when `isDefinitelyNotVietnamese` fires.
  - Handled `isInstantRestore` in `XKeyIMController.swift` by immediately calling `commitComposition()`, which clears the marked text underline.
- **Result**: English words are now restored to plain text and committed automatically.
