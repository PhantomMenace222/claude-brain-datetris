---
name: Default Flutter run devices
description: Default device for `flutter run -d` is iPhone 17 Simulator; real iPhone 26 + Chrome web are alternates with specific use cases
type: reference
originSessionId: 0fd2af8c-49e2-4f6d-87a1-6fb69cb21b93
---
When forming `flutter run -d <device>` commands (per the M81 launch-
command rule), pick the device per this table — never leave the
selection to the user.

| Use case | Device | UDID / arg |
|---|---|---|
| **Default** — UI work, business logic, API integration | iPhone 17 Simulator | `841E07BF-ABB0-4FB2-8D01-E7CC0648CDFC` |
| Real-device features — mic, camera, push notifications, App Store auth flows | iPhone 26 (wireless) | `00008140-0018688C3602801C` |
| Web build smoke test — CORS, web-only fallback paths | Chrome | `chrome` (use `--web-port 8088`, no UDID) |

Rule: **default to the iPhone 17 simulator** unless the test
specifically needs a real-device capability listed above. Voice-like
recording is the most common reason to switch to iPhone 26 wireless
(simulator mic is unreliable).

### Decision tree (apply per test ask)

| Test scope | Device | Reason |
|---|---|---|
| Pure UI / navigation / state changes | **iPhone 17 simulator** (default) | Fast boot, hot-reload, no cable needed |
| Voice record / mic features | iPhone 26 wireless | Simulator mic is unreliable |
| Camera / image upload (real capture flow, not picker stubs) | iPhone 26 wireless | Real camera hardware |
| Push notifications | iPhone 26 wireless | APNs doesn't deliver to simulator |
| Apple Pay / IAP | iPhone 26 wireless | Sandbox StoreKit requires real device |
| Reproducing prod-only bugs (works on sim, breaks in prod) | iPhone 26 wireless | Closer to real-user environment |

When the PM (Claude.ai chat) asks the user to test something, **default
to simulator** unless one of the rows above applies. **State explicitly
in the launch-command block which device was chosen and why** so the
user can override at a glance — e.g.:

> "Using iPhone 26 wireless — voice-like needs real mic."
> "Using iPhone 17 simulator — pure UI change, no hardware."

Never silently pick the real device when the simulator would do; cable
+ provisioning + slower boot is friction we should only ask for when
the test actually needs it.

Examples:
```bash
# Default test
flutter run -d 841E07BF-ABB0-4FB2-8D01-E7CC0648CDFC

# Real-mic test (voice notes, push notifications)
flutter run -d 00008140-0018688C3602801C

# Web-build verification
flutter run -d chrome --web-port 8088
```

If the user adds a new device, update this file rather than scattering
new UDIDs across other memories.
