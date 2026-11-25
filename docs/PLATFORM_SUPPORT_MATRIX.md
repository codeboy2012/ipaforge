# Platform Support Matrix

**Status: nothing in this matrix is verified.** No upstream source, build, SDK check, or device test has been
performed for this fork. Every row is a *target to investigate*, not a capability.

Three separate questions are tracked here and they must never be merged:

1. **Host** — which desktop operating system and CPU architecture can build and run the app.
2. **Device** — which iOS device architectures the tooling can produce/verify output for, and under which signing
   and profile conditions.
3. **Connection** — how a host reaches a device, and whether that path is supported by upstream and by the OS pair.

A host being "able to run the app" says nothing about it being able to sign, and neither says anything about which
device can be installed to.

## Evidence levels

| Label | Meaning |
| --- | --- |
| **Verified** | Reproduced on named host + OS build + upstream commit (+ device, where relevant), with logs recorded in this repository. |
| **Planned** | In scope for a roadmap phase; no attempt made. |
| **Investigating** | Plausible and being examined; blocked on vendoring, an SDK check, or a licence/technical unknown. |
| **Not yet tested** | Technically conceivable, no attempt made, and no evidence held either way. |
| **Unsupported** | Positively ruled out with recorded evidence and a stated reason. |
| **TBD** | Decision or fact not yet determined. |

Transitions: only *Verified* may ever appear in user-facing copy as "supported".

---

## 1. Desktop host targets

All rows are **proposed targets to investigate**. The fork does not yet have a toolchain, so no host can build it.

| # | Host OS | CPU arch | Status | Why it is in scope | Validation work required before any claim |
| --- | --- | --- | --- | --- | --- |
| H1 | Windows 10/11 | x86_64 | **Planned / investigating** | A large share of Impactor-style users are on Windows; upstream may already carry a Windows build path. | Confirm upstream builds with the available MSVC/MinGW toolchain. Confirm device transport (Apple Mobile Device service / usbmuxd) is detected. Confirm code-signing path works without macOS. Check long paths, permissions, packaging, and 64-bit helper handling. |
| H2 | Windows 10/11 | x86 (32-bit) | **Planned / investigating — feasibility low** | Requested target; relevant only for old hardware. | Determine whether upstream or its Apple device service supports a 32-bit host process at all (64-bit drivers/services generally cannot be loaded by a 32-bit process). Check address-space limits on large IPA processing. If either fails, mark **Unsupported** with the evidence. |
| H3 | macOS | Intel x86_64 | **Planned / investigating** | Native `codesign`, `security`, and profile tooling are available; likely the reference host. | Confirm upstream builds against current Xcode command-line tools. Verify signing/verification of modified bundles. Confirm Gatekeeper/notarisation behaviour for the packaged app. Check any x86-only helper dependencies. |
| H4 | macOS | Apple Silicon arm64 | **Planned / investigating** | Primary modern developer host; native ARM64 toolchain. | Confirm native arm64 build (or Rosetta 2 for x86-only components, documented as such). Verify universal-binary handling in inspected app bundles. Verify signing/export and notarisation on arm64. |
| H5 | Linux | x86_64 | **Planned / investigating — high risk** | Popular with researchers; open-source friendly. | Determine whether upstream supports Linux at all. Confirm device access via `libimobiledevice`/`usbmuxd`. **Resolve the signing question:** is a profile-based signature even producible/verifiable without Apple's tooling for the intended flows? If not, document a degraded, inspection-only scope or mark unsupported. |
| H6 | Linux | ARM64 / ARM (armhf) | **Planned / investigating — highest risk** | Low-cost ARM boards and ARM laptops. | Everything in H5, plus: availability of upstream dependencies and Apple device libraries on ARM, package/distro coverage, performance on large bundles, and the same signing question. |

**Not evaluated:** Windows on ARM64, BSD, and mobile/tablet hosts. They are out of scope for now and are *not*
implicitly supported by the rows above.

**Cross-cutting host requirements to establish (TBD):** minimum OS versions, minimum SDK/toolchain versions, required
Apple device service version, disk-space and memory envelope, and whether a host can build without network access.

---

## 2. iOS device architecture targets

Device support depends on four things at once: **upstream code paths**, **SDK/toolchain slices**, **signing and
provisioning-profile support**, and **tested hardware**. None of these has been established. All rows are
**not yet established**.

| Device arch | Typical iOS era | Status | What must be established (all four, with evidence) |
| --- | --- | --- | --- |
| **arm64** | iOS 7 and later, 64-bit devices | **Investigating — not yet tested** | (1) Upstream produces/handles arm64 slices. (2) Toolchain can sign arm64. (3) Profile/entitlement set is valid for the target iOS version. (4) Install reproduced on a named device + iOS build, with logs. |
| **arm64e** | A12-class and later (iOS 12+) with pointer authentication | **Investigating — not yet tested** | As above, plus: whether the signing path preserves arm64e structures and pointer-authentication metadata; whether profile/device combination accepts the result; explicit test on arm64e hardware. Until then, **no arm64e compatibility is claimed** and the UI must show an unknown state. |
| **armv7 / armv7s** (legacy 32-bit) | iOS 10 and earlier (last 32-bit devices: iPhone 5/5c, iPad 4) | **Not yet tested — likely unsupported** | Whether a 32-bit-capable SDK and signing path still exist at all; whether upstream retains 32-bit code paths; availability of physical test hardware running a supported iOS version; whether profiles can still be issued for those devices. If any is unavailable, mark **Unsupported** with the reason. |

Notes:

- Simulator slices (x86_64 / arm64 simulator) inside embedded frameworks are **not device support** and must not be
  presented as such. They may still appear during inspection and should be labelled "simulator".
- Fat binaries may contain a mix of architectures; support must be assessed per slice, never per file.
- iOS **version** support is a separate axis from architecture and remains **TBD**. No iOS range is claimed. Modern
  versions may require Developer Mode, current pairing mechanisms, and current profile rules that upstream may not
  implement.

---

## 3. Connection compatibility

Connection is host × device × transport. Nothing below has been attempted.

| # | Connection path | Hosts | Status | Notes / validation needed |
| --- | --- | --- | --- | --- |
| C1 | USB via `usbmuxd` / `libimobiledevice` (jailed device) | macOS, Linux | **Investigating — not yet tested** | Confirm upstream uses this path; confirm pairing-record creation and trust prompt; confirm Developer Mode requirement on modern iOS; test service availability and recovery after cable pull. |
| C2 | USB via Apple Mobile Device service (`usbmuxd` on Windows) | Windows x86_64, x86 (if H2 survives) | **Investigating — not yet tested** | Requires Apple device support installed and its service running; confirm detection, trust, and install. Note that 32-bit hosts (H2) may not interoperate with 64-bit drivers/services. |
| C3 | CoreDevice / `remoted`-style transport (newer Xcode device stack) | macOS | **Investigating — not yet tested** | Modern iOS versions may require this rather than classic usbmuxd. Unknown whether upstream implements it; if absent, this may hard-cap the iOS versions reachable and must be documented as a limitation, not worked around with claims. |
| C4 | Wi-Fi / network pairing | macOS, Windows, Linux (if upstream supports) | **Investigating — not yet tested** | Depends on reused pairing records and mDNS discovery; historically unreliable. Validate only if upstream exposes it; otherwise list as unsupported-by-upstream. |
| C5 | Local network / SSH to a jailbroken device | any | **Out of current scope** | Not a target for this fork. Would require device-side tooling the project does not ship, and would expand the security surface. Explicitly excluded from the support matrix. |

**Device preconditions to verify per test:** trust/pairing completed, Developer Mode enabled where required, the
device unlocked, passcode state recorded, and the device's iOS build and model identifier captured in the report.
Device identifiers must be redacted before anything is committed.

---

## 4. Validation plan

Each target is promoted only through a recorded test. The minimum evidence for any "Verified" label:

1. **Environment record** — host OS and build, CPU arch, toolchain versions, device model identifier, iOS build,
   upstream commit SHA, fork commit SHA.
2. **Build** — build unmodified upstream from a clean checkout; save the full log.
3. **Detect** — device appears with correct model/OS/architecture.
4. **Pair/trust** — pairing completes; failure mode recorded if it does not.
5. **Install** — install an unmodified build of an app the tester owns; record result and any error text.
6. **Round trip** — inspect → modify (one cosmetic, one structural) → sign → install. Record what was verified and
   what remained unknown.
7. **Negative cases** — profile missing an entitlement, expired profile, mismatched bundle ID, unsigned bundle,
   unsupported architecture. All must fail *visibly and correctly*.
8. **Redaction** — strip UDIDs, serials, emails, real paths, team identifiers, and certificate names before committing
   logs or screenshots.
9. **Matrix update** — change the row's status and link the record. A status change without a linked record is invalid.

### Per-target task backlog

| Target | Open validation tasks |
| --- | --- |
| H1 Windows x86_64 | Build path, device transport, signing without macOS, packaging, permissions |
| H2 Windows x86 | 32-bit host feasibility, Apple service interop, memory limits |
| H3 macOS Intel | Build against current CLT, signing/verification, notarisation, x86 helper deps |
| H4 macOS arm64 | Native build, Rosetta dependency audit, universal-binary handling, signing |
| H5 Linux x86_64 | Upstream support, libimobiledevice path, **signing feasibility**, packaging |
| H6 Linux ARM | All of H5 plus dependency/SDK availability and performance |
| arm64 device | Slice handling, signing, profile validity, install on real hardware |
| arm64e device | Pointer-auth-safe signing, structure preservation, install on A12+ hardware |
| 32-bit device | SDK existence, upstream 32-bit paths, usable profile, physical hardware |
| C1–C4 | Transport detection, trust, Developer Mode, recovery, upstream implementation status |

---

## 5. What this project will not claim

- A platform is supported because it compiles.
- A device is compatible because its architecture appears in a Mach-O list.
- An install works because a device was detected once.
- A newer iOS version works because an older one did.
- A signing flow is portable because a signature file was produced.
- Any of the above on the strength of a mockup, a plan, or an upstream README.

**Current summary: 0 verified hosts, 0 verified device architectures, 0 verified connection paths.**
