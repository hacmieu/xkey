# Fix Apple Settings Fallback to 20260721 Build

**Date:** 2026-08-16 14:40

- **Cause:** macOS System Settings scans `/Applications/XKey.app` when adding Input Methods via UI. An old `/Applications/XKey.app` from 2026-07-21 existed on disk, overriding `~/Library/Input Methods/XKeyIM.app` when re-added.
- **Fix:** Copied updated `Release/XKey.app` (version `20260816`) to `/Applications/XKey.app` and `~/Library/Input Methods/XKeyIM.app`.
- **Script Update:** Updated `build_release.sh` to sync `/Applications/XKey.app` and re-register LaunchServices (`lsregister`) automatically.
