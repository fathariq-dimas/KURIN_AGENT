# Requirements — Integrated Onboarding, Health Profile, Assessment, and Initial Plan

> **Feature ID:** `001`  
> **Product sources:** `docs/guide/02-target-user.md` §§2.4–2.5; `docs/guide/03-training-types.md` §3.3; `docs/guide/04-weekly-training-plan.md` §§4.1–4.6; `docs/guide/14-end-to-end-training-flow.md` Phase 1; `docs/guide/17-bmi-formula.md` §§1–3.3; explicit user decisions dated 2026-11-10  
> **Feature directory:** `docs/Feature/001-onboarding-health-profile/`  
> **Prepared by:** `@planner`  
> **Status:** `Approved — explicit user approval recorded 2026-11-10`
> **Approval authority:** Explicit user approval, 2026-11-10; the listed v1 screening catalogs and disclaimer copy are authoritative for specification authoring.

## 1. Overview

Feature 001 owns the complete first-run journey from Health Screening through creation and local persistence of an
Initial Training Plan. It includes profile confirmation, BMI and weight-goal presentation, Training Type Assignment,
schedule configuration, plan review, and completion. Feature IDs 002 and 003 remain reserved; their former assignment
and initial-plan responsibilities are integrated into Feature 001.

The flow starts with Health Screening. It does not include a separate name-entry step or workout reminder/time setup.
Real-time exercise guidance and every post-session evaluation or adjustment remain downstream behavior.

The v1 screening catalogs intentionally use the approved placeholder-labelled entries documented in Section 8, and the
v1 disclaimer uses the approved text in `FR-06`. These values are authoritative for v1 specification and planning.
Product should later replace or review them, but that follow-up is non-blocking and does not reopen this approval.

## 2. Purpose

- Identify a reported diagnosis, active symptom, or ongoing injury before recommending an initial training track.
- Let the user complete onboarding when HealthKit is skipped, unavailable, or cannot return required profile values.
- Calculate and present BMI and an informational normal-BMI target-weight range from confirmed profile values.
- Assign a conservative Easy Walking baseline whenever screening reports an issue or unrounded BMI is at least 30.
- Generate a deterministic partial or full first-week plan from the assigned track and the user's allowed schedule.
- Persist the confirmed onboarding result locally before the onboarding flow is complete.

## 3. Actors and Preconditions

### Actors

| Actor | Responsibility | Source |
|---|---|---|
| User | Completes screening, chooses whether to use HealthKit, confirms or enters profile values, sets a target weight, configures the allowed schedule, reviews the plan, and confirms completion. | Guide 2 §2.4; user decisions 2026-11-10 |
| Kurin iOS app | Orchestrates onboarding, evaluates screening and BMI, assigns the training type, generates the Initial Training Plan, and persists the confirmed result. | Guides 2–4; Guide 14 Phase 1; user decisions 2026-11-10 |
| Apple Health / HealthKit | Supplies readable date of birth, height, and body weight when the user chooses to request access. | Guide 2 §2.4; Rule 01 HealthKit; user decision 2026-11-10 |

### Preconditions

| Precondition | Unmet behavior | Source |
|---|---|---|
| Onboarding is not already complete for the local user. | Start Feature 001 at the diagnoses screening stage. | User decision 2026-11-10 |
| The user can provide a valid past date of birth and positive height and body weight, either from readable HealthKit values or manual entry. | Keep profile confirmation incomplete until valid required values are provided. | Guide 2 §2.4; Guide 17 §1.2; user decision 2026-11-10 |

## 4. Functional Requirements

| ID | Requirement | Source |
|---|---|---|
| `FR-01` | The first-run flow must begin with Health Screening and must not require name entry or workout reminder/time setup. | Guide 2 §2.4 planning decision; Guide 14 Phase 1; user decision 2026-11-10 |
| `FR-02` | Health Screening must present three ordered stages: diagnosed conditions, active symptoms, then ongoing injuries. Each stage must use a Product/Clinical-supplied closed multi-select catalog with an exclusive `None` choice and no free-text entry. | Guide 2 §2.4; user decision 2026-11-10 |
| `FR-03` | Selecting `None` must clear and exclude every other selection in that stage. Selecting any non-`None` item must clear `None`. | User decision 2026-11-10 |
| `FR-04` | `hasHealthIssue` must be true when at least one non-`None` diagnosis, symptom, or injury is selected, and false only when all three stages resolve to `None`. | Guide 3 §3.3; user decision 2026-11-10 |
| `FR-05` | When `hasHealthIssue` is true, the app must display the non-blocking informational Health Disclaimer, require no acknowledgement, and continue automatically to HealthKit choice. When false, it must skip the disclaimer. | Guide 2 §2.4; user decision 2026-11-10 |
| `FR-06` | The v1 disclaimer must display exactly: “Thank you for sharing your information. Good recovery is the priority.” | User approval 2026-11-10 |
| `FR-07` | Feature 001 must request read access only for date of birth, height, and body weight. Other health and activity metrics must be deferred to the future feature that uses them. | Rule 01 HealthKit; user decision 2026-11-10 |
| `FR-08` | The user must be able to skip the HealthKit request and continue by entering date of birth, height, and body weight manually. | Guide 2 §2.4; user decision 2026-11-10 |
| `FR-09` | If HealthKit is unavailable or its request or query fails, the app must offer both Retry and Continue Manually. | Guide 2 §2.4; user decision 2026-11-10 |
| `FR-10` | Missing HealthKit values must be described as unavailable or unreadable; the app must not claim that it detected per-type read denial. | Guide 2 §2.4 HealthKit privacy note |
| `FR-11` | Readable HealthKit date of birth, height, and body weight must prefill the profile form and remain editable before confirmation. | Guide 2 §2.4 |
| `FR-12` | A value entered or edited manually must remain local, must not be written back to HealthKit, and must be identified as `MANUAL`; an unedited readable HealthKit value must be identified as `HEALTH`. | Rule 01 HealthKit; user decision 2026-11-10 |
| `FR-13` | Date of birth must be a valid past date with no age eligibility gate. Height and current body weight must each be greater than zero. | Guide 17 §1.2; user decision 2026-11-10 |
| `FR-14` | BMI must be calculated using `weightKilograms / (heightMeters × heightMeters)`. Classification and training assignment must use the unrounded result, while user-facing BMI must display one decimal place. | Guide 17 §§1, 3.3; user decision 2026-11-10 |
| `FR-15` | BMI categories must be continuous: underweight for BMI `< 18.5`, normal for `18.5 ≤ BMI < 25`, overweight for `25 ≤ BMI < 30`, and obesity for BMI `≥ 30`. | Guide 17 §2; user decision 2026-11-10 |
| `FR-16` | The user must be able to enter any target weight greater than zero. The app must calculate the informational normal-BMI range as `18.5 × heightMeters²` through `24.9 × heightMeters²` and display both bounds to one decimal place. The range must not block the selected target. | Guide 2 §2.4; Guide 17 §3.1; user decision 2026-11-10 |
| `FR-17` | The Weight Goal Overview must show the confirmed profile values, BMI value and category, target weight, and informational recommended range. | Guide 2 §2.4; Guide 17 §3.1 |
| `FR-18` | Final Assessment must assign HR Zone 2 Activity only when `hasHealthIssue` is false and unrounded BMI is less than 30. Underweight and normal-BMI users may continue through this rule. | Guide 3 §3.3; Guide 17 §§2, 3.2; user decision 2026-11-10 |
| `FR-19` | Final Assessment must assign Easy Walking when `hasHealthIssue` is true or unrounded BMI is at least 30. | Guide 3 §3.3; Guide 17 §§2, 3.2; user decision 2026-11-10 |
| `FR-20` | Easy Walking must be fixed at 3 sessions per week and 15 minutes per session during onboarding. The user may choose only its workout weekdays and start day. | Guide 3 §3.3; Guide 4 §§4.1–4.2 |
| `FR-21` | HR Zone 2 Activity must allow 3, 4, or 5 sessions per week. Session duration must be selectable in 5-minute increments from 15 minutes through `300 / frequency` minutes, inclusive. | Guide 3 §3.3; Guide 4 §§4.1–4.2; user decision 2026-11-10 |
| `FR-22` | Workout weekdays must be unique, and their count must equal the selected or assigned weekly frequency. | Guide 4 §§4.1, 4.5; user decision 2026-11-10 |
| `FR-23` | The user must choose the program start weekday from the selected workout weekdays, and the resolved start date must not be in the past. | Guide 4 §4.5 |
| `FR-24` | If the chosen start weekday matches the onboarding weekday, the app must offer Start Today or Same Day Next Week. | Guide 4 §4.5 |
| `FR-25` | If the chosen start weekday differs from the onboarding weekday, the app must use its nearest future occurrence without offering an additional deferral choice. | Guide 4 §4.5 |
| `FR-26` | Initial-plan date generation must use a Gregorian Monday–Sunday week. A current-week start must include only selected dates from the start date through Sunday; a next-week start must include all selected weekdays in that Monday–Sunday week, including selected days before the anchor date. | Guide 4 §§4.4–4.6; user decision 2026-11-10 |
| `FR-27` | Scheduled sessions must be represented as local date-only values. A later time-zone change must not shift an already scheduled session to a different calendar day. | User decision 2026-11-10 |
| `FR-28` | Before completion, the app must show a review screen containing the assigned training type, frequency, duration, selected weekdays, resolved start date, and all first-week session dates. | Guide 14 Phase 1; user decision 2026-11-10 |
| `FR-29` | The user must explicitly confirm the reviewed plan before the complete screening, profile, goal, assignment, schedule, and Initial Training Plan are saved atomically to local storage. | Guide 14 Phase 1; user decision 2026-11-10 |
| `FR-30` | Onboarding must become complete only after the atomic save succeeds. A save failure must keep the current flow available, show a recoverable failure, and allow a same-flow retry without reporting completion. | User decision 2026-11-10 |
| `FR-31` | An incomplete onboarding draft must not persist across app relaunch. Relaunching before successful completion must restart at the diagnoses screening stage. | User decision 2026-11-10 |

## 5. Non-Functional Requirements

| ID | Constraint | Verification | Source |
|---|---|---|---|
| `NFR-01` | Feature 001 must support iOS 27 on iPhone 17 using SwiftUI, Dynamic Type, semantic colors, and accessibility labels for every interactive control. | Builder build and accessibility review after all gates pass. | Rule 01 iOS 27; Rule 05 SwiftUI |
| `NFR-02` | New asynchronous flow must use Swift 6 `async/await` with explicit actor isolation and no `DispatchQueue` flow control. | Strict-concurrency build and static review after handoff. | Rule 05 Concurrency |
| `NFR-03` | The feature must preserve `View → ViewModel → concrete Service → concrete Repository → HealthKit/SwiftData`; repositories and services must not use protocol abstractions, ViewModels must call services only, and Views must contain no business logic. | Architecture review after Rule 02 is synchronized and before builder handoff. | AGENTS.md §2; Rule 07 §7; user decision 2026-11-10 |
| `NFR-04` | All profile, screening, goal, and plan data must remain local; raw health data must not be sent to iCloud or a remote backend. | Data-flow and entitlement review. | Rule 01 HealthKit; AGENTS.md §7 |
| `NFR-05` | Feature 001 Domain services must achieve at least 80% line coverage in the actual iOS test target. Every AC must map to an automated test or objective UI/accessibility check. | Coverage report and AC verification mapping after handoff. | Rule 03; user decision 2026-11-10 |

## 6. Acceptance Criteria

| ID | Requirement | Given | When | Then |
|---|---|---|---|---|
| `AC-01` | `FR-01`, `FR-02` | A new onboarding flow | The user begins onboarding | The first screen is diagnoses, followed only by symptoms and then injuries; no name or reminder/time step appears. |
| `AC-02` | `FR-02`, `FR-03`, `FR-04` | The three authoritative v1 closed catalogs are loaded | The user selects `None` in every stage | Other selections are cleared, no free-text input exists, and `hasHealthIssue` is false. |
| `AC-03` | `FR-02`–`FR-04` | An authoritative v1 catalog is loaded | The user selects any non-`None` item in diagnoses, symptoms, or injuries | `None` is cleared for that stage and `hasHealthIssue` is true. |
| `AC-04` | `FR-05`, `FR-06` | `hasHealthIssue` is true | Screening completes | The exact v1 informational disclaimer appears, requires no acknowledgement, and continues automatically to HealthKit choice. |
| `AC-05` | `FR-05` | `hasHealthIssue` is false | Screening completes | The disclaimer is skipped and HealthKit choice appears. |
| `AC-06` | `FR-07` | Screening and any disclaimer are complete | The user chooses to request HealthKit access | The request contains DOB, height, and body weight reads only. |
| `AC-07` | `FR-08` | HealthKit choice is visible | The user chooses Skip | No HealthKit request occurs and manual DOB, height, and body-weight entry is available. |
| `AC-08` | `FR-09`, `FR-10` | HealthKit is unavailable or a request/query fails | The attempt ends | Retry and Continue Manually are offered, and no message claims detected per-type read denial. |
| `AC-09` | `FR-10`–`FR-12` | HealthKit returns readable DOB, height, and body weight | Profile confirmation appears | Values are prefilled, editable, identified as `HEALTH`, and no broader health metric is shown or requested. |
| `AC-10` | `FR-11`, `FR-12` | HealthKit returns only some required values | Profile confirmation appears | Readable values are prefilled and only missing values require manual entry with `MANUAL` provenance. |
| `AC-11` | `FR-12` | A HealthKit-prefilled profile value is visible | The user edits it | The edited local value is identified as `MANUAL` and is not written back to HealthKit. |
| `AC-12` | `FR-13` | Profile confirmation is visible | DOB is today/future or height/current weight is zero or negative | Confirmation is prevented; any valid past DOB is accepted without an age gate. |
| `AC-13` | `FR-14`, `FR-15` | Height is 170 cm and weight is 86.7 kg | BMI is calculated | The unrounded result is classified as obesity at the 30 boundary and displayed as `30.0 kg/m²`. |
| `AC-14` | `FR-15` | Valid profile pairs yield BMI values immediately below, at, and immediately above 18.5, 25, and 30 | Classification runs | Every value maps continuously to exactly one approved category with no gap or overlap. |
| `AC-15` | `FR-16`, `FR-17` | Height is 170 cm and the target weight is positive | The user opens Weight Goal Overview | The informational range displays as `53.5–72.0 kg`, the target is not rejected for being outside that range, and all overview fields appear. |
| `AC-16` | `FR-16` | Weight Goal entry is visible | The user enters a zero or negative target | Continuation is prevented. |
| `AC-17` | `FR-18` | Screening has no issue and unrounded BMI is below 30, including an underweight or normal result | Final Assessment runs | HR Zone 2 Activity is assigned. |
| `AC-18` | `FR-19`, `FR-20` | Any non-`None` screening item is selected and BMI is below 30 | Final Assessment runs | Easy Walking is assigned and remains fixed at 3 sessions/week × 15 minutes. |
| `AC-19` | `FR-19`, `FR-20` | Screening has no issue and unrounded BMI is exactly 30 | Final Assessment runs | Easy Walking is assigned and remains fixed at 3 sessions/week × 15 minutes. |
| `AC-20` | `FR-20`, `FR-22` | Easy Walking is assigned | Schedule setup appears | Frequency and duration cannot be changed, and exactly three unique weekdays are required. |
| `AC-21` | `FR-21` | HR Zone 2 Activity is assigned | The user configures frequency and duration | Frequencies 3/4/5 allow only 5-minute values in 15–100, 15–75, and 15–60 minutes respectively. |
| `AC-22` | `FR-22` | A frequency is selected or assigned | The weekday selection contains duplicates or its unique count differs from frequency | Continuation is prevented. |
| `AC-23` | `FR-23`, `FR-24` | Onboarding is Thursday and Thursday is a selected workout day | Thursday is chosen as the start weekday | Start Today and Same Day Next Week are offered. |
| `AC-24` | `FR-23`, `FR-25` | Onboarding is Thursday and Saturday is a selected workout day | Saturday is chosen as the start weekday | The nearest future Saturday is used with no additional deferral choice. |
| `AC-25` | `FR-26` | Onboarding is Thursday; Monday, Thursday, and Saturday are selected | Start Today is chosen | The Gregorian current-week plan contains Thursday and Saturday only. |
| `AC-26` | `FR-26` | Onboarding is Thursday; Tuesday, Thursday, and Saturday are selected | Same Day Next Week is chosen | The Gregorian next-week plan contains Tuesday, Thursday, and Saturday, including Tuesday before the Thursday anchor. |
| `AC-27` | `FR-27` | A session is scheduled for a local calendar date | The device time zone later changes | The session remains assigned to the same calendar date. |
| `AC-28` | `FR-28` | A valid assessment and schedule have generated the Initial Training Plan | Review appears | It shows training type, frequency, duration, weekdays, resolved start date, and every first-week session date. |
| `AC-29` | `FR-29`, `FR-30` | The review is valid | The user confirms and the atomic local save succeeds | All required onboarding data and sessions are saved together before onboarding reports completion. |
| `AC-30` | `FR-30` | The review is valid | The user confirms and the atomic save fails | Onboarding remains incomplete, the current flow remains available, a recoverable error appears, and retry is offered. |
| `AC-31` | `FR-31` | Onboarding has not completed successfully | The app relaunches | No draft is restored and onboarding restarts at diagnoses screening. |

## 7. Out of Scope

| Excluded item | Reason | Source |
|---|---|---|
| Separate Feature 002 or Feature 003 implementations | Training Type Assignment and Initial Training Plan generation are integrated into Feature 001; IDs 002/003 are reserved. | `docs/README.md`; user decision 2026-11-10 |
| Name collection and workout reminder/time setup | They are omitted from the approved Feature 001 flow. | Guide 2 §2.4 planning decision; Guide 14 Phase 1; user decision 2026-11-10 |
| HealthKit reads beyond DOB, height, and body weight | Future features request their own required metrics contextually. | Rule 01 HealthKit; user decision 2026-11-10 |
| Persisting or resuming an incomplete onboarding draft after relaunch | The approved flow restarts until an atomic completion succeeds. | User decision 2026-11-10 |
| Real-time workout preparation, monitoring, and guidance | These begin after the Initial Training Plan exists. | Guide 14 Phases 2–4 |
| RPE, training load, workload metrics, daily adjustment, and recurring weekly adjustment | These are downstream post-session responsibilities. | Guide 14 Phases 5–10 |
| Medical diagnosis, treatment, or assurance that exercise is safe | Screening selects a conservative product track; it is not clinical clearance. | Guides 2–3 |
| Nutrition, strength training, social features, subscriptions, and remote backend sync | Explicit product exclusions. | AGENTS.md §7 |

## 8. Dependencies and Open Decisions

### Resolved User Decisions

| ID | Decision | Authority |
|---|---|---|
| `UD-01` | Restart the documentation workflow only; preserve dated product decisions. Feature 001 integrates onboarding, assignment, and Initial Training Plan; IDs 002/003 remain reserved; omit name and reminder/time. | Explicit user decision, 2026-11-10 |
| `UD-02` | Use three ordered closed multi-select screening catalogs, no free text, and an exclusive `None`; every positive selection sets `hasHealthIssue`, assigns Easy Walking, and triggers a non-blocking automatic disclaimer. | Explicit user decision, 2026-11-10 |
| `UD-03` | Use continuous BMI categories, unrounded decision values, one-decimal UI values, minimal positive-value validation, no age gate, and an informational target range. | Explicit user decision, 2026-11-10 |
| `UD-04` | Request only DOB, height, and body weight during onboarding; allow Skip, Retry, and Continue Manually; keep HealthKit read-only and manual edits local. | Explicit user decision, 2026-11-10 |
| `UD-05` | Use 5-minute Zone 2 duration increments, unique weekday count equal to frequency, Gregorian Monday–Sunday weeks, and date-only sessions that do not shift after time-zone changes. | Explicit user decision, 2026-11-10 |
| `UD-06` | Require review and explicit confirmation, atomic local save, same-flow retry, and restart after relaunch for incomplete onboarding. | Explicit user decision, 2026-11-10 |
| `UD-07` | Use concrete repositories/services without protocols and require at least 80% Feature 001 Domain-service line coverage; synchronize Rule 02 before builder handoff. | Explicit user decision, 2026-11-10 |
| `UD-08` | Replace historical drafts in place only at their eligible approval gate; create no duplicate canonical docs. | Explicit user decision, 2026-11-10 |
| `UD-09` | Promote the listed placeholder-labelled screening entries and disclaimer text to authoritative v1 content, close the requirements gate, and permit specification authoring. | Explicit user approval, 2026-11-10 |

### Dependencies

| Direction | Dependency | Required contract | Source |
|---|---|---|---|
| Product input | V1 screening catalogs | Use the authoritative stable IDs and labels listed below, including each stage's exclusive `None`. | User approval 2026-11-10 |
| Product input | V1 Health Disclaimer content | Use the exact approved text in `FR-06` with non-blocking automatic continuation. | User approval 2026-11-10 |
| External | HealthKit | Read-only access for DOB, height, and body weight with privacy-preserving unavailable behavior. | Guide 2 §2.4; Rule 01; user decision 2026-11-10 |
| External | Local persistence | Atomic local save of the confirmed onboarding result; no remote synchronization. | `FR-29`, `FR-30`; user decision 2026-11-10 |
| Governance | Architecture rules | Rule 02 must be synchronized to the approved concrete repository/service rule before builder handoff. | User decision 2026-11-10 |
| Downstream | Feature 004 Exercise Session Guidance | Consumes the assigned Training Type and ordered Initial Training Plan session dates/durations after Feature 001 completes. | Guide 14 Phases 1–3 |

### Authoritative V1 Screening Content

| Catalog / content | Authoritative v1 stable ID or copy | Status |
|---|---|---|
| Diagnosed conditions | `diagnosis.pending-product-01` — “Diagnosis option pending Product/Clinical review”; `diagnosis.none` — “None” | Authoritative for v1 |
| Active symptoms | `symptom.pending-product-01` — “Symptom option pending Product/Clinical review”; `symptom.none` — “None” | Authoritative for v1 |
| Ongoing injuries | `injury.pending-product-01` — “Injury option pending Product/Clinical review”; `injury.none` — “None” | Authoritative for v1 |
| Health Disclaimer | “Thank you for sharing your information. Good recovery is the priority.” | Authoritative for v1 |

### Open Decisions

None. The user closed all material requirement decisions for v1 on 2026-11-10.

### Non-Blocking Product Follow-up

Product/Clinical should replace the placeholder-labelled catalog entries with production clinical content and review
the disclaimer wording in a later requirement revision. Until that revision is explicitly approved, the v1 IDs,
labels, and disclaimer text above remain authoritative. This follow-up does not block specification authoring,
roadmap preparation after specification approval, or builder handoff under the current approved v1 scope.

## 9. Traceability

| Requirement | Acceptance criteria | Product source |
|---|---|---|
| `FR-01`–`FR-06` | `AC-01`–`AC-05` | Guide 2 §2.4; Guide 3 §3.3; user decisions 2026-11-10 |
| `FR-07`–`FR-12` | `AC-06`–`AC-11` | Guide 2 §2.4; Rule 01 HealthKit; user decisions 2026-11-10 |
| `FR-13`–`FR-17` | `AC-12`–`AC-16` | Guide 17 §§1–3.3; user decisions 2026-11-10 |
| `FR-18`–`FR-21` | `AC-17`–`AC-21` | Guide 3 §3.3; Guide 4 §§4.1–4.2; Guide 17 §3.2; user decisions 2026-11-10 |
| `FR-22`–`FR-27` | `AC-22`–`AC-27` | Guide 4 §§4.4–4.6; user decisions 2026-11-10 |
| `FR-28`–`FR-31` | `AC-28`–`AC-31` | Guide 14 Phase 1; user decisions 2026-11-10 |

## 10. Quality Gate

- [x] Feature boundary, integrated ownership, and downstream exclusions are explicit.
- [x] Every FR maps to at least one acceptance criterion.
- [x] Screening, HealthKit, BMI, assignment, schedule, date, and completion behavior cite exact evidence or user decisions.
- [x] HealthKit scope follows current-feature least privilege and preserves read-authorization privacy.
- [x] No template comments or angle-bracket placeholders remain.
- [x] No specification, roadmap, phase-plan, code, build, or test work was performed during this requirements stage.
- [x] The user promoted the listed screening IDs/labels and disclaimer text to authoritative v1 content.
- [x] No material v1 open decision remains.
- [x] The user reviewed and explicitly approved these requirements on 2026-11-10.
- [ ] Rule 02 has been synchronized to the approved concrete repository/service architecture before builder handoff.

**Gate result:** Requirements approved on 2026-11-10. Specification authoring is authorized. Roadmap/phase planning
remains locked until the specification receives separate explicit user approval.
