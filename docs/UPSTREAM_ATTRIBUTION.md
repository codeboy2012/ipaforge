# Upstream Attribution & Licensing — Verification Required

> **This document is a checklist, not a licence record.** No upstream source is present in this repository, so no
> licence has been read, no copyright line has been confirmed, and no obligation has been met. Every field below is
> **unverified** until it is filled in from the actual vendored files and reviewed.

The fork is intended to build on two upstream projects. Their real identities, licences, and completeness must be
established when the source is added — not assumed from memory, from a package index, or from a blog post.

## 1. Upstream inventory (to be completed at vendoring)

| Field | iLoader | Impactor |
| --- | --- | --- |
| Canonical repository URL | **TBD — verify** | **TBD — verify** |
| Mirror / fork lineage (which is the true upstream) | **TBD — verify** | **TBD — verify** |
| Pinned commit SHA | **TBD — verify** | **TBD — verify** |
| Fetch date | **TBD** | **TBD** |
| Licence file path in tree | **TBD — verify** | **TBD — verify** |
| SPDX identifier | **TBD — verify** | **TBD — verify** |
| Copyright holder(s) as written | **TBD — verify** | **TBD — verify** |
| NOTICE file present? | **TBD** | **TBD** |
| Source complete and buildable as published? | **TBD** | **TBD** |
| Bundled third-party components | **TBD** | **TBD** |
| Trademark / naming policy | **TBD** | **TBD** |
| Redistribution constraints (SDKs, signing assets) | **TBD** | **TBD** |

Do not fill any cell from memory. Copy from the files under `vendor/` once they exist, and link to the exact path and
line count of the licence text.

## 2. Capture procedure when the source is added

1. Vendor the sources **unmodified** into `vendor/<upstream>/`, preserving their own directory structure.
2. Copy each licence file verbatim into the vendored tree if it is not already there; never rewrite or reformat it.
3. Record in `PROVENANCE.md`: URL, commit SHA, fetch method, fetch date, tree hash if available, and whether any local
   patch was applied (patches must live in their own tracked files, applied at build time, never mixed into the
   vendored tree).
4. Inventory bundled third-party code and assets, with their licences.
5. Identify any code derived from Apple SDK headers, sample code, or reverse-engineered protocol descriptions, and note
   the redistribution implications.
6. Produce `THIRD_PARTY_NOTICES.md` at the repository root.
7. Have the licence conclusion reviewed by someone competent to do so before any binary is published.

## 3. Obligations by licence family (contingency, not a conclusion)

| If upstream turns out to be… | Then the fork must… |
| --- | --- |
| **Permissive** (MIT / BSD-2 / BSD-3 / ISC) | Preserve copyright and permission notices in source and in binary distributions; include the licence text; do not imply endorsement. |
| **Apache-2.0** | Preserve `NOTICE`; state modified files; include the licence text; honour the patent grant and termination terms. |
| **LGPL (2.1 / 3.0)** | Keep the library separable, provide the licence and corresponding source for the licensed parts, and allow relinking. Static linking raises additional questions — resolve before packaging. |
| **GPL (2.0 / 3.0)** | Distribute the whole combined work under the same licence, with source, on any binary release. This directly constrains a closed-source product and must be decided before design work assumes otherwise. |
| **AGPL** | As GPL, plus the network-use condition, which can reach hosted components. |
| **Proprietary / no licence / "all rights reserved"** | No redistribution and no derivative distribution. Vendoring locally for evaluation may still be permitted, but shipping is not. Stop and escalate. |
| **Mixed / unclear** | Treat as the strictest component present until clarified. Do not ship. |

Additional universal items:

- **Modification disclosure.** Most licences require that changed files carry prominent notices and dates. Plan for a
  `MODIFICATIONS.md` that lists each diverged file and why.
- **Name and marks.** "IPAForge" is a working title. Upstream project names and logos may be subject to trademark or
  project policy; use them only in factual attribution ("builds on …") unless permission is confirmed. Never present
  the fork as endorsed by an upstream maintainer or by Apple.
- **Apple materials.** Apple SDKs, headers, signing certificates, and provisioning profiles are not redistributable by
  this project. The repository must contain no Apple binaries, no `.p12`, no `.mobileprovision`, no team tokens.
- **Export control / encryption.** If upstream includes cryptographic code, review export classification before
  distributing binaries. *TBD.*
- **Patent posture.** Apache-2.0 and some BSD variants carry explicit patent terms; record whether any apply.

## 4. This repository's own licence

**Undecided and intentionally unset.** The fork's licence cannot be chosen until §1 is complete, because a copyleft
upstream dictates the licence of the combined work. Until then, no `LICENSE` file is added and the default
"all rights reserved" applies to new material authored here (documentation, mockups, icon concepts), subject to
maintainer decision.

## 5. Verification checklist (all unchecked)

- [ ] Upstream repository URLs confirmed against the actual source delivered, and pinned by commit SHA
- [ ] Every licence file copied verbatim and its SPDX identifier recorded
- [ ] Copyright holders recorded exactly as written
- [ ] Third-party components and their licences inventoried
- [ ] NOTICE / attribution requirements satisfied in source **and** in any packaged artefact
- [ ] Copyleft implications assessed and accepted by the maintainer before any publication
- [ ] Modification disclosure plan in place (`MODIFICATIONS.md` + per-file notices if required)
- [ ] Trademark / naming review for "iLoader", "Impactor", and "IPAForge"
- [ ] Confirmation that no Apple SDK, certificate, profile, or proprietary asset is redistributed
- [ ] Export-control review completed if cryptographic code is present
- [ ] `THIRD_PARTY_NOTICES.md` and root `LICENSE` added
- [ ] Written maintainer approval recorded before any binary, installer, or release is published

## 6. Current public claim

> IPAForge is an independent, unaffiliated work-in-progress fork that intends to build on the iLoader and Impactor
> projects. It is not affiliated with, endorsed by, or supported by those projects or by Apple. All licensing and
> attribution statements remain to be verified against the source once it is added.
