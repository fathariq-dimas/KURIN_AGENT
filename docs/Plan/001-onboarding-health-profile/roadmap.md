# Master Roadmap — Integrated Onboarding, Health Profile, Assessment, and Initial Plan

> **Feature ID:** `001`  
> **Feature docs:** `docs/Feature/001-onboarding-health-profile/`  
> **Output path:** `docs/Plan/001-onboarding-health-profile/roadmap.md`  
> **Prepared by:** `@planner`  
> **Status:** `Draft — blocked by missing implementation workspace and architecture-rule synchronization`

## 1. Goal and Feature Boundary

Deliver one ordered first-run experience that starts with diagnoses screening and completes only after the user has
reviewed and atomically saved a locally stored Initial Training Plan. The roadmap owns the integrated onboarding,
Training Type Assignment, and Initial Training Plan responsibilities approved for Feature 001.

### Included

- Diagnoses → active symptoms → ongoing injuries screening with authoritative v1 closed catalogs and exclusive None.
- Deterministic `hasHealthIssue`, exact non-blocking disclaimer copy, and automatic no-acknowledgement continuation.
- Optional, read-only HealthKit access for DOB, height, and body mass, with Skip/Retry/manual recovery.
- Profile confirmation, provenance, continuous BMI categories, target weight, and informational target range.
- HR Zone 2 Activity or Easy Walking assignment and track-constrained schedule configuration.
- Gregorian Monday–Sunday, local date-only first-week plan generation.
- Review, explicit confirmation, atomic local save, same-flow retry, and restart after relaunch for incomplete flows.
- AC-mapped tests and accessibility/architecture verification for each eligible vertical phase.

### Excluded and Downstream

- Feature IDs 002 and 003 have no separate implementation; their initial responsibilities are integrated here.
- Name collection, workout reminder/time setup, remote sync, and persisted incomplete onboarding drafts.
- HealthKit reads beyond DOB, height, and body mass; future features request their own data contextually.
- Feature 004 owns exercise preparation and guidance after consuming the assigned type and scheduled sessions.
- Features 005–008 own RPE, training load/workload, and daily/weekly adjustment.
- The non-blocking Product follow-up may replace the placeholder-labelled v1 catalogs and review disclaimer copy only
  through a later approved requirements revision; current v1 content remains authoritative meanwhile.

## 2. Dependency and Output Contracts

| Direction | Feature or system | Contract | Evidence/source | Status |
|---|---|---|---|---|
| Source | Approved Feature 001 requirements | `FR-01`–`FR-31`, `AC-01`–`AC-31`, authoritative v1 content, and resolved decisions | `docs/Feature/001-onboarding-health-profile/requirement.md` | Approved 2026-11-10 |
| Source | Approved Feature 001 specification | Concrete data/service/view contracts, state machine, formulas, errors, and complete test matrix | `docs/Feature/001-onboarding-health-profile/spec.md` | Approved 2026-11-10 |
| External | HealthKit | Optional read-only DOB, height, and body-mass access with privacy-safe unavailable behavior | Requirement `FR-07`–`FR-12`; spec §§3, 6, 9 | Required in Phase 02 |
| External | SwiftData/local persistence | One atomic local save for the completed aggregate; rollback on failure | Requirement `FR-29`, `FR-30`; spec §§2, 6, 7 | Required in Phase 05 |
| Platform | Gregorian local-date calendar | Monday–Sunday arithmetic and date-only values that do not shift after time-zone changes | Requirement `FR-23`–`FR-27`; spec §§2, 4, 5 | Required in Phase 04 |
| Governance | Architecture rules | Concrete repositories/services without protocols; Rule 02 must be synchronized to that approved direction | Requirement `NFR-03`; spec §9 | `GOV-01` blocks builder handoff |
| Workspace | Xcode/source/test tree | Restore `Kurin.xcodeproj`, Swift source, and the iOS test target; reconcile canonical paths, schemes, target membership, and executable build/test/coverage commands | AGENTS.md; approved spec §§1, 9 | `RB-01` blocks plan readiness and builder handoff |
| Git | Phase 01 branch contract | Base `development` tracks `origin/development` at `dd3204e`; intended target `feat/001-health-screening-flow` does not exist and must not be created before phase approval | Rule 06a; verified Git state 2026-11-10; Phase 01 metadata | Base verified; target creation pending phase approval |
| Output | Feature 004 Exercise Session Guidance | Persisted assigned `TrainingType` and ordered Initial Training Plan session dates/durations | Requirement §8; Guide 14 Phases 1–3 | Produced after Phase 05 |

## 3. Phase Status

Use only: `Draft`, `Ready for Review`, `Approved`, `In Progress`, `Verification`, or `Complete`.

| Phase | Reviewable outcome | FR/AC coverage | Depends on | Plan review | Implementation | Verification | Completion evidence |
|---|---|---|---|---|---|---|---|
| `01 — Health Screening and Disclaimer` | Ordered v1 screening produces the correct issue flag and exact automatic disclaimer/no-disclaimer transition. | `FR-01`–`FR-06`; `AC-01`–`AC-05` | None | Draft | Draft | Draft | Pending `RB-01`, `GOV-01`, reconciled verification procedures, and explicit phase approval |
| `02 — Health Access, Profile, and BMI` | User skips or uses the exact HealthKit scope, completes only missing profile values, and receives correct provenance/BMI. | `FR-07`–`FR-15`; `AC-06`–`AC-14` | Phase 01 Complete | Draft | Draft | Draft | Locked until Phase 01 Complete |
| `03 — Weight Goal and Overview` | User enters a positive target and reviews the complete informational goal overview. | `FR-16`–`FR-17`; `AC-15`–`AC-16` | Phase 02 Complete | Draft | Draft | Draft | Locked until Phase 02 Complete |
| `04 — Assignment, Schedule, and Initial Plan` | Correct training type, allowed schedule, start date, and time-zone-stable first-week sessions are generated. | `FR-18`–`FR-27`; `AC-17`–`AC-27` | Phase 03 Complete | Draft | Draft | Draft | Locked until Phase 03 Complete |
| `05 — Review, Persistence, and Completion` | User reviews and confirms the plan; atomic save controls completion/retry and incomplete relaunch restart. | `FR-28`–`FR-31`; `AC-28`–`AC-31` | Phase 04 Complete | Draft | Draft | Draft | Locked until Phase 04 Complete |

Coverage audit: the five phases cover every `FR-01`–`FR-31` and `AC-01`–`AC-31` exactly once, with no gap or
overlap.

## 4. Open Decisions and Blockers

There are no material product or technical open decisions in the approved v1 requirements/specification.

| ID | Decision or blocker | Owner | Affected phase | Status |
|---|---|---|---|---|
| `RB-01` | Restore or attach `Kurin.xcodeproj`, the `Kurin/` Swift source tree, and `KurinTests/` iOS test target; then reconcile canonical paths, schemes, target membership, and exact executable build/test/coverage commands. | User / Engineering | `01`–`05` | Blocking plan readiness, verification, and builder handoff |
| `GOV-01` | Synchronize Rule 02 with the approved concrete repository/service, no-protocol architecture before builder handoff. | Architecture owner | `01`–`05` | Blocking builder handoff |

The Product catalog/copy follow-up is explicitly non-blocking and is not an open decision for v1.

## 5. Current Phase Gate

- **Current eligible phase:** `01 — Health Screening and Disclaimer`
- **Detailed phase plan:** `phase-01-health-screening-and-disclaimer.md`
- **User approval:** Not eligible while `RB-01` prevents source/target/verification reconciliation; phase remains Draft
- **Builder handoff:** Locked by `RB-01`, `GOV-01`, and missing explicit phase approval

The next phase remains locked until `@planner` verifies the current phase's AC, build/test, blocker, and required
user-review evidence and marks it `Complete`.
