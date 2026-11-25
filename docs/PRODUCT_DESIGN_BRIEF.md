# IPAForge — Product & Design Brief

**Status:** concept brief for a pre-development fork. No code exists. Everything here describes intent.
**Related:** [README](../README.md) · [Platform support matrix](PLATFORM_SUPPORT_MATRIX.md) · [Upstream attribution](UPSTREAM_ATTRIBUTION.md)

---

## 1. Product thesis

Sideloading and re-signing tools tend to fail in two directions: they either hide what they are doing behind a single
"Install" button, or they expose raw internals with no explanation of which choices are safe.

IPAForge takes the position that the value is in **legibility**. A user should be able to see what is inside a bundle,
understand which edits a provisioning profile will and will not permit, and get a reviewable report of exactly what
changed before anything is signed or installed.

> If the tool cannot verify a thing, it says *unknown*. It does not say *okay*.

## 2. Who it is for

| Persona | Needs | What IPAForge should give them |
| --- | --- | --- |
| **App developer (own builds)** | Inspect what shipped, test a variant, produce a signed build without the full IDE | Fast inspection, deterministic export, checks that mirror Xcode's expectations |
| **Power user / researcher** | Understand an app they own and make small deliberate changes | Explicit, reversible modification plan plus a clear statement of what is unverified |
| **Support / QA engineer** | Confirm a build is signed correctly and will install | Pre-flight checks with pass / fail / unknown, plus a saved report |
| **Contributor evaluating the fork** | Know what is real and what is aspiration | Documentation where every unverified claim is labelled |

Not a persona: anyone looking to install or modify software they are not authorised to use. That is out of scope.

## 3. Jobs to be done

1. *"Tell me what this IPA actually is before I touch it."*
2. *"Change this one field without breaking the signature in a way I can't see."*
3. *"Does my profile allow what I'm trying to do — and what exactly will be dropped?"*
4. *"Give me a signed bundle and a record of what you verified."*
5. *"If this fails, tell me why in language I can act on."*

## 4. Experience principles

1. **Evidence over assurance.** Every check has three states — pass, fail, unknown — and unknown is styled as a
   first-class outcome, never as a pass.
2. **Stage, then commit.** Edits accumulate in a visible plan. Nothing on disk changes until export.
3. **Constraints sit next to the choice.** The provisioning-profile rule appears beside the option it constrains, not
   in a help page.
4. **No surprise network.** Inspection and editing are local. Any future network feature must be explicit and opt-in.
5. **Destructive actions are named.** "Remove extension", "drop entitlement", "replace signature" — no euphemisms.
6. **Credentials are boring and invisible.** Profiles and identities are chosen from local storage; they are never
   displayed in full, logged, or uploaded.
7. **Calm visual hierarchy.** One primary action per screen. Colour is used for state, not decoration.

## 5. Core flows (concept)

### Import → Inspect
Drop an `.ipa`. The tool shows bundle identity, versions, architectures, embedded components, and signature state.
The inspector separates *facts read from the bundle* from *claims that need a profile or device to evaluate*.

### Inspect → Plan
Options are grouped by risk: cosmetic (name, icon), structural (bundle ID, resources), and touchy (code injection,
extension removal). Anything profile-dependent states its dependency inline and stays unvalidated until review.

### Plan → Review
A diff-like summary — what will change, what will be dropped, what cannot be checked. The primary button states the
consequence ("Sign & export"), not an abstraction ("Continue").

### Review → Sign → Export
The export writes once, then re-reads the result and re-verifies it. The report is exportable text so a failure can be
attached to an issue without screenshots full of personal data.

### Device (optional, later)
Detect, summarise capability, install, log. Only shown for host/device combinations that have actually been validated.

## 6. Information architecture (proposed)

```text
Library      imported IPAs, recent files, drag-in target
Inspect      metadata · binary & architectures · components · profile · entitlements · resources
Modify       staged change plan, grouped by risk
Sign         identity selection, entitlement reconciliation, pre-flight, export
Devices      detection, pairing status, capability summary  (investigating)
Log          session activity, redaction on export
Settings     output paths, appearance, diagnostics
```

A single window with a persistent left rail and a contextual inspector panel; the inspector is a sibling pane rather
than a modal so users can compare a value with the change they are making.

## 7. Visual language

| Token | Value | Use |
| --- | --- | --- |
| `bg/base` | `#0B0E13` | window background |
| `bg/surface` | `#121A23` | cards, panels |
| `bg/raised` | `#16202B` | selected rows, inputs |
| `line/subtle` | `#26313F` | dividers, borders |
| `text/primary` | `#E8EEF6` | headings, values |
| `text/muted` | `#93A1B3` | labels, captions |
| `accent/forge` | `#F0A14B` | primary action, active state |
| `accent/steel` | `#5AD1C4` | secondary highlight, links |
| `state/pass` | `#57C98B` | verified only |
| `state/attention` | `#F0A14B` | pending, requires input |
| `state/danger` | `#F2685F` | failure, destructive action |
| `state/unknown` | `#93A1B3` | not checked, not testable |

- **Typography:** system UI stack (SF / Segoe UI / Inter fallback) for interface text; a monospace stack for
  identifiers, hashes, and paths, because those values are compared character by character.
- **Shape:** 10–14 px radii on cards and controls, 6 px on chips, no fully-rounded "pill" buttons except status chips.
- **Density:** comfortable, one idea per row; the tool is read slowly, not scanned quickly.
- **Motion:** minimal — state changes only, no ambient animation. This is a utility, not a showcase.
- **Iconography:** geometric, 2 px stroke, single accent colour; icon concepts are in
  [`assets/brand/`](../assets/brand/).

### Brand directions explored

1. **Stack & Spark** — layered plates standing for bundle layers, with a forge spark. Reads well at 16 px, most
   product-like, safest for an app icon.
2. **Layered Monogram** — a geometric `I`/`F` cut from two plates. Strongest wordmark pairing, slightly more abstract
   at small sizes.
3. **Keystone Shield** — a shield containing a bundle plate. Most explicitly about trust and signing, least distinctive
   as a mark.

No direction is chosen. All three are labelled concepts, and none is derived from upstream or Apple iconography.

## 8. Copy and tone

- Plain, specific, short. Prefer "Not checked — no device connected" to "Something went wrong".
- Never use a success word for an unperformed check.
- Use "you" for the user's machine, "this device" for hardware, "profile" and "identity" precisely.
- Every mockup or screenshot shown publicly carries a *concept* or *sample data* label.

## 9. Accessibility targets (design intent, not verified)

- Minimum 4.5:1 contrast for body text on surfaces; state chips pair colour with text, never colour alone.
- Full keyboard traversal of the change plan with visible focus rings.
- Status announced in text for screen readers ("Pass", "Unknown", "Needs attention").
- Respect reduced-motion and increased-contrast preferences.
- These are targets to test once a UI exists; **no accessibility audit has been performed**.

## 10. Success measures (proposed)

- A user can answer "will my profile allow this?" without leaving the review screen.
- Zero support claims in the repository that are not backed by a recorded validation result.
- An exported report is sufficient for another person to reproduce a failure.
- First-run to first successful inspect under two minutes on a fully supported host (target, unmeasured).

## 11. Non-goals

- Bulk piracy workflows, paid-app extraction, DRM research tooling.
- Entitlement editing beyond profile-granted subsets.
- Cloud accounts, sync, telemetry, or any upload of user bundles.
- Store distribution, device management (MDM), or enterprise fleet features.
- Beating upstream at installation reliability on unvalidated platforms.

## 12. Decisions still open

| Decision | Why it is open |
| --- | --- |
| Desktop shell and UI toolkit | Must follow what upstream actually is; unknown until P1 |
| Supported iOS range | Cannot be asserted without upstream code, SDK, signing path, and hardware tests |
| Signing backend on non-Apple hosts | May be infeasible; would cap the Linux targets |
| Final name and licence | Blocked on upstream licence review |
| Whether a CLI ships | Depends on upstream architecture and packaging effort |
