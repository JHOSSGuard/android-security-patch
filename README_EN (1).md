# JHOSSGuard Security Patch for Android 13+

## Overview
This patch introduces **JHOSSGuard**, a security mechanism designed to mitigate privilege escalation attempts by untrusted applications in Android 13+ (Tiramisu and later).

## Key Features
- **ActivityManagerService (AMS) Hardening**
  - Adds `enforceNoPrivilegeEscalationLocked` method to detect and block cross-user or unauthorized service calls without the `INTERACT_ACROSS_USERS_FULL` permission.
  - Integrated into `startService` (and can be extended to other sensitive calls like `broadcastIntentLocked`).

- **SELinux Policy Reinforcement**
  - Adds new rules (`jhossguard.te`) under `system/sepolicy/private/`.
  - Blocks apps (`appdomain`) from resolving critical services (`activity`, `package`, `usagestats`, `batterystats`, `procstats`).
  - Prevents untrusted apps from adding new services to `system_server`.

## Impact
- Prevents spyware-like apps or bloatware from abusing hidden privileges.
- Strengthens user/system separation, reducing attack surface for privilege escalation.
- Provides compatibility with Android 13+.

## Applying the Patch
1. Clone or sync AOSP source tree (Android 13+).
2. Copy the patch into the source root.
3. Run:
   ```bash
   git apply --3way JHOSSGuard_Security_A13.patch
   ```
4. Rebuild relevant modules:
   ```bash
   source build/envsetup.sh
   lunch aosp_arm64-eng
   m services sepolicy -j$(nproc)
   ```

## Testing
- Deploy on emulator or test device running Android 13+.
- Attempt to start/broadcast cross-user services from a non-privileged app → should be blocked with `SecurityException`.
- Verify SELinux denials via `adb logcat` and `dmesg`.

## Notes
- This patch is designed for **educational and research purposes**.
- Requires integration testing before production deployment.
- Contributions welcome via GitHub pull requests.

---
Author: **JHOSSGuard**
