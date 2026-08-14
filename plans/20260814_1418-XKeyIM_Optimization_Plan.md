# XKeyIM Debug Review — Optimization Plan

**Date:** 2026-08-14 14:18  
**Status:** Plan created

## Goal
Tối ưu XKeyIM dựa trên debug log và diff review của ngày 2026-08-14.

## Tasks

### P0 — Verify Cursor Tracking Fix
- [ ] User test `dd ow`, `thoong`, `heer`, `cuar` in VS Code
- [ ] If still broken: refine `skipNextCursorCheck` logic
- [ ] If working: commit changes

### P1 — Macro IMKit Tests
- [ ] Add test: macro expansion in marked-text mode
- [ ] Add test: `addSpaceAfterMacro` true/false behavior
- [ ] Add test: macro in direct mode (overlay apps)

### P2 — Log Noise Reduction
- [ ] Add duration (ms) to `[TIMING]` logs
- [ ] Reduce `[OVERLAY]` logging to once per app activation
- [ ] Add log-level filtering to `DebugLogger`

### P3 — English Retry Telemetry
- [ ] Design privacy-preserving event schema (local hash only)
- [ ] Emit event when English candidate is composed then corrected
- [ ] Build analyzer to surface frequently retried tokens

### P4 — Build & Signing
- [ ] Document Debug deploy command
- [ ] Either fix Release signing or add CI script

## Success Criteria
- Cursor fix verified by user
- Macro IMKit has regression tests
- Log analyzer can identify hot spots
- No plaintext user content in retry telemetry