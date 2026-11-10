# 2. Target User

The application is targeted at overweight individuals who want to lose weight but have not yet developed a consistent exercise routine or do not understand appropriate training workloads suited to their physical capabilities.

### 2.1. User Characteristics

1. Overweight, particularly in the pre-obese category.
2. Desires rapid weight loss.
3. Lacks a regular exercise habit, including individuals who have never exercised or haven't exercised in a long time.
4. Prone to pushing themselves too hard when starting to exercise.
5. Lacks an understanding of appropriate exercise intensity, duration, and frequency suited to their physical capabilities.

### 2.2. User Pain Points

1. Heart rate frequently spikes to excessively high intensities during workouts.
2. Experiences extreme fatigue after exercising.
3. Feels that workout sessions are overly strenuous and uncomfortable.
4. Struggles to determine or structure an exercise plan tailored to their physical capabilities.

### 2.3. Potential Impacts

1. Increased injury risk due to inappropriate training loads.
2. Potential health risks when engaging in activities exceeding physical capacity or incompatible with medical conditions.
3. Difficulty maintaining exercise consistency.
4. Failure to build long-term exercise habits.

### 2.4. Onboarding, Health Screening & Profile Setup

Before the Initial Training Plan is generated, the user goes through an onboarding process to collect profile information, complete health screening, and establish target weight goals.

Onboarding flow:

1. **Health Screening:** The system inquires about diagnostic history, active symptoms, and ongoing injuries.
2. **Health Disclaimer (conditional):** A disclaimer screen is shown if the Health Screening flow directs to it.
3. **Apple Health Permission:** The system requests permission to read Apple Health data.
4. **Profile Input:** The user confirms or completes date of birth, height, and weight.
5. **Weight Goal:** The user enters a target weight, with recommended weight ranges based on normal BMI.
6. **Weight Goal Overview:** The system displays a profile summary, target weight, and BMI category information.
7. **Final Assessment:** The system determines the Training Type track as well as frequency and duration options according to the user's criteria (Section 3.3 and Section 4).
8. The user selects allowed frequency/duration values, training days, and a start date before the system creates the Initial Training Plan. Easy Walking remains fixed at 3 sessions per week and 15 minutes per session while the user chooses the three weekdays.

> **Planning decision (2026-11-10):** Health Screening is the first onboarding activity. A separate name-entry step and workout-reminder time are not part of the authoritative Feature 001 flow. Training Type Assignment and Initial Training Plan creation are owned by Feature 001 rather than separate Feature 002/003 implementations.

**Data planned to be retrieved from Apple Health:**

- Heart Rate (HR).
- Resting Heart Rate (RHR).
- Heart Rate Variability (HRV).
- Blood Oxygen Saturation (SpO2).
- Respiratory rate.
- Body weight.
- Height.
- Date of birth.
- Calories (the exact HealthKit energy quantity remains a product decision).
- Step count.
- Walking/running distance (the exact distance scope remains a product decision).
- Activity duration (the source quantity or workout aggregation remains a product decision).

When Apple Health returns readable data, the form pre-populates profile values as defaults that the user can confirm or update. Physiological and activity values are read for downstream assessment and activity context; the product behavior for displaying each of those values during onboarding remains to be specified. If HealthKit is unavailable, the authorization request fails, or a required profile value is not readable after the authorization attempt, the app provides manual entry for date of birth, height, and weight. Manual entry is not required for Heart Rate, Resting Heart Rate, HRV, SpO2, respiratory rate, calories, steps, distance, or activity duration during onboarding.

HealthKit read-authorization privacy must be preserved: the app cannot determine whether read permission was granted or denied for an individual data type, and `authorizationStatus(for:)` does not reveal read authorization. A missing value may reflect denial, absence of data, or another unreadable condition, so user-facing copy and internal state must describe data as unavailable or not readable rather than claiming that read access was denied. Source: Apple Developer Documentation, **Authorizing access to health data** and `authorizationStatus(for:)`.

> **Planning note (user decision, 2026-11-10):** The expanded Apple Health scope above is authoritative for onboarding planning. The exact HealthKit quantity used for “calories,” the distance categories included, and the derivation of “activity duration” are unresolved and must be decided before implementation.

### 2.5. Body Mass Index (BMI) Calculation & Classification

During onboarding profile setup, BMI (*Indeks Massa Tubuh* / IMT) is calculated from the user's confirmed height and weight. BMI determines the user's weight classification, calculates recommended target-weight boundaries, and informs Training Type assignment during the Final Assessment.

For the comprehensive specification, worked examples, and precision rules, see [BMI Formula & Classification](17-bmi-formula.md).

#### 2.5.1. BMI Calculation Formula

```text
BMI = Weight (kg) / (Height (m))^2
```

When height is captured in centimeters:

```text
BMI = Weight (kg) / (Height (cm) / 100)^2
```

#### 2.5.2. Classification Table

| Kategori IMT | Rentang Nilai IMT (kg/m²) | System Impact |
| :--- | :---: | :--- |
| **Kekurangan berat badan (Underweight)** | `< 18,5` | Eligible for HR Zone 2 Activity (if screening passes) |
| **Berat badan normal (Normal weight)** | `18,5 – 24,9` | Baseline range for recommended target weight ($18.5 \times \text{Height}^2$ to $24.9 \times \text{Height}^2$) |
| **Kelebihan berat badan (Overweight)** | `25,0 – 29,9` | Primary Kurin target user; eligible for HR Zone 2 Activity (if screening passes) |
| **Obesitas (Obesity)** | `≥ 30,0` | Mandates assignment to **Easy Walking** baseline ($3\text{ sessions/week} \times 15\text{ min}$) |

