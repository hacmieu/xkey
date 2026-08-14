# English Token Retry Optimization

**Date:** 2026-08-14 11:10  
**Status:** Proposed

## Goal

Prevent Vietnamese Telex processing from corrupting frequently corrected English tokens such as `adsense`, and use local logs to prioritize real user pain points.

## Design

1. Add a failing `VNEngine` regression test proving `adsense` remains literal in Telex.
2. Add a conservative per-keystroke English-candidate decision that does not affect incomplete Vietnamese syllables.
3. Emit privacy-preserving local telemetry only when an English candidate is composed and immediately corrected: stable local hash, length, trigger key, and correction type; never plaintext token.
4. Extend the log analyzer to group retries by local hash and count, with a minimum threshold before reporting.
5. Run focused engine tests, then the XKey test suite.

## Success criteria

- `adsense` stays literal while Telex is active.
- Existing Vietnamese composition, especially `dd` -> `đ` and `lys` -> `lý`, remains correct.
- Logs do not contain plaintext typed tokens for retry telemetry.
- Analyzer identifies recurrent correction candidates by count.