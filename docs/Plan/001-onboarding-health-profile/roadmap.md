# Master Roadmap — Integrated Onboarding, Health Profile, Assessment, and Initial Plan

> **Feature ID:** `001`  
> **Feature docs:** `docs/Feature/001-onboarding-health-profile/`  
> **Output path:** `docs/Plan/001-onboarding-health-profile/roadmap.md`  
> **Prepared by:** `@planner`  
> **Status:** `Draft — canonical roadmap; builder handoff locked`

This roadmap supersedes `docs/Plan/001-initial-training-plan-generation.md` for future review and handoff. The
legacy flat plan remains preserved as historical context and must not be sent to `@builder`.

## 1. Goal and Feature Boundary

Deliver one ordered first-run experience that starts with Health Screening and ends only after a safe training type,
allowed schedule, and Initial Training Plan have been atomically persisted.

### Included

- Diagnoses → symptoms → injuries screening and conditional Health Disclaimer.
- Apple Health read request, privacy-preserving fallback, and required profile confirmation.
- BMI calculation/classification and Weight Goal Overview.
- HR Zone 2 Activity or Easy Walking assignment.
- Track-constrained frequency, duration, weekday, and start-date configuration.
- Partial/full first-week session generation, recommendation review, persistence, and ready state.
- All models, services, presentation, and tests formerly associated with reserved IDs 002 and 003.

### Excluded and Downstream

- Separate Feature 002/003 implementations; those IDs are reserved and absorbed into Feature 001.
- Name entry and workout reminder time.
- Feature 004 real-time session preparation/guidance and all post-session Features 005–008.
- Medical diagnosis/treatment, remote sync, nutrition, strength training, subscriptions, and social features.

## 2. Dependency and Output Contracts

| Direction | Feature or system | Contract | Evidence/source | Status |
|---|---|---|---|---|
| Input | Screening catalog / disclaimer policy | Stable selection IDs, localized labels, trigger mapping, copy, and acknowledgement rule. | Requirement `OD-01`, `OD-02` | Blocking Phase 01 |
| External | HealthKit | Approved read identifiers, readable-value queries, and no per-type read-denial inference. | Guide 2 §2.4; requirement `OD-04`, `OD-05` | Blocking Phase 02 details |
| External | SwiftData/local persistence | Atomic save for the completed onboarding aggregate. | `FR-26`, `AC-22`, `AC-23` | Defined; architecture shape blocked by `OD-08` |
| Input | Calendar/time zone | Deterministic Monday–Sunday and non-past start-date calculations. | Guide 4 §§4.4–4.6; requirement `OD-07` | Blocking Phase 04 |
| Output | Feature 004 Exercise Session Guidance | Assigned `TrainingType` and ordered scheduled Initial Training Plan sessions. | Guide 14 Phases 1–3 | Defined |
| Output | Profile/progress presentation | Confirmed profile, BMI/category, target weight, and normal-BMI reference range. | Guide 2 §2.4; Guide 17 | Defined |

## 3. Phase Status

Use only: `Draft`, `Ready for Review`, `Approved`, `In Progress`, `Verification`, or `Complete`.

| Phase | Reviewable outcome | FR/AC coverage | Depends on | Plan review | Implementation | Verification | Completion evidence |
|---|---|---|---|---|---|---|---|
| `01 — Health Screening and Disclaimer` | User completes diagnoses → symptoms → injuries; the issue flag and approved disclaimer branch are independently verifiable. | `FR-01`–`FR-04`; `AC-01`–`AC-03` | None | Draft | Draft | Draft | Pending `OD-01`, `OD-02`, `OD-08`, source-tree reconciliation, and user approval |
| `02 — Health Access, Profile, and BMI` | User receives readable profile defaults or completes only missing DOB/height/weight manually; BMI is calculated and classified. | `FR-05`–`FR-11`; `AC-04`–`AC-09` | Phase 01 Complete | Draft | Draft | Draft | Pending Phase 01 and `OD-03`–`OD-05`, `OD-08` |
| `03 — Weight Goal and Overview` | User enters a target and reviews profile, BMI, target, and normal-BMI reference range. | `FR-12`, `FR-13`; `AC-10` | Phase 02 Complete | Draft | Draft | Draft | Pending Phase 02 and `OD-03`, `OD-08` |
| `04 — Assessment, Schedule, and Initial Plan` | User receives the correct track, configures only allowed schedule fields, and obtains deterministic first-week sessions. | `FR-14`–`FR-23`; `AC-11`–`AC-20` | Phase 03 Complete | Draft | Draft | Draft | Pending Phase 03 and `OD-07`, `OD-08` |
| `05 — Recommendation, Persistence, and Ready` | User reviews the plan, edits allowed fields, confirms it, and reaches ready only after an atomic save. | `FR-24`–`FR-27`; `AC-21`–`AC-23` | Phase 04 Complete | Draft | Draft | Draft | Pending Phase 04 and `OD-08`; draft resume follows `OD-06` |

## 4. Open Decisions and Blockers

| ID | Decision or blocker | Owner | Affected phase | Status |
|---|---|---|---|---|
| `OD-01` | Approve the diagnosis, symptom, and injury catalogs plus none/free-text behavior. | Product / clinical | `01` | Blocking |
| `OD-02` | Approve disclaimer triggers, copy, and acknowledgement behavior. | Product / clinical / legal | `01` | Blocking |
| `OD-03` | Approve DOB/height/weight/target validation and target-range display rounding. | Product / clinical | `02`, `03` | Blocking |
| `OD-04` | Approve exact calories, distance, and activity-duration HealthKit identifiers/aggregation. | Product / HealthKit | `02` | Blocking |
| `OD-05` | Approve recency/statistics and display/persistence scope for expanded health/activity metrics. | Product / privacy | `02`, `05` | Blocking for expanded scope |
| `OD-06` | Decide whether incomplete onboarding is persisted and resumable. | Product | `01`–`05` | Non-blocking for happy-path slices |
| `OD-07` | Approve Calendar/time-zone/locale/DST policy. | Product / engineering | `04` | Blocking |
| `OD-08` | Resolve concrete-only repositories versus narrow Domain repository protocols. | Architecture owner | `01`–`05` | Blocking handoff |
| `RB-01` | Reconcile planned paths, schemes, and test targets with an available Xcode source tree before implementation. | Engineering / `@planner` | `01`–`05` | Blocking handoff |
| `RB-02` | Select and validate a base branch under Rule 06a before branch creation. | User / `@builder` | `01` | Blocking handoff |

## 5. Current Phase Gate

- **Current eligible phase:** `01 — Health Screening and Disclaimer`
- **Detailed phase plan:** `phase-01-health-screening-and-disclaimer.md`
- **User approval:** Pending after `OD-01`, `OD-02`, `OD-08`, `RB-01`, and `RB-02` are resolved
- **Builder handoff:** Locked

The next phase remains locked until `@planner` verifies the current phase's AC, build/test, blocker, and required
user-review evidence and marks it `Complete`.
