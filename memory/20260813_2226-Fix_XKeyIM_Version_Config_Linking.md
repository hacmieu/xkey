# Fix XKeyIM Version.xcconfig Linking

**Date:** 2026-08-13 22:26

- Fixed missing `baseConfigurationReference` linking to `Version.xcconfig` in target `XKeyIM` within `project.pbxproj`.
- Verified build version update: `CFBundleVersion` successfully changed from `20260721` to `20260813`.
- Rebuilt and reinstalled `XKeyIM.app` to `~/Library/Input Methods/`.
