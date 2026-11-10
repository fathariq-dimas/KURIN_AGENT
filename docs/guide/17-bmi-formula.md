# Body Mass Index (BMI) Formula & Classification

Body Mass Index (BMI), or *Indeks Massa Tubuh* (IMT), is a standardized physiological metric that assesses body mass relative to height. Within Kurin, BMI is evaluated during onboarding to determine weight categories, calculate recommended target weight ranges, and assign the user's initial training track.

---

## 1. BMI Calculation Formula

The Body Mass Index is calculated by dividing body weight in kilograms by the square of height in meters:

```text
BMI = Weight (kg) / (Height (m))^2
```

### 1.1. Calculation with Centimeters

When height is recorded in centimeters (standard in Apple Health and Kurin UI input fields), height is converted to meters before squaring:

```text
Height (m) = Height (cm) / 100
BMI = Weight (kg) / (Height (cm) / 100)^2
```

Equivalently:

```text
BMI = (Weight (kg) * 10,000) / (Height (cm))^2
```

### 1.2. Units & Input Requirements

| Parameter | Metric Unit | HealthKit Quantity Identifier | Required Range / Constraints |
| :--- | :--- | :--- | :--- |
| **Weight** | Kilograms (`kg`) | `HKQuantityTypeIdentifier.bodyMass` | Strictly positive (`> 0`) |
| **Height** | Meters (`m`) or Centimeters (`cm`) | `HKQuantityTypeIdentifier.height` | Strictly positive (`> 0`) |
| **BMI** | `kg/m²` | Computed property / `HKQuantityTypeIdentifier.bodyMassIndex` | Strictly positive (`> 0`) |

### 1.3. Worked Examples

- **Example 1 (Normal Weight):**
  - Height: 175 cm ($1.75\text{ m}$)
  - Weight: 68 kg
  - $\text{BMI} = \frac{68}{1.75^2} = \frac{68}{3.0625} \approx 22.20\text{ kg/m}^2$
  - Classification: **Berat badan normal (Normal weight)**

- **Example 2 (Overweight / Pre-obese):**
  - Height: 170 cm ($1.70\text{ m}$)
  - Weight: 78 kg
  - $\text{BMI} = \frac{78}{1.70^2} = \frac{78}{2.89} \approx 26.99\text{ kg/m}^2$
  - Classification: **Kelebihan berat badan (Overweight)**

- **Example 3 (Obesity Boundary):**
  - Height: 170 cm ($1.70\text{ m}$)
  - Weight: 86.7 kg
  - $\text{BMI} = \frac{86.7}{2.89} = 30.0\text{ kg/m}^2$
  - Classification: **Obesitas (Obesity)**

---

## 2. Table of Classification

The classification categories and BMI value ranges are defined below:

| Kategori IMT | Rentang Nilai IMT (kg/m²) | Category (EN) | System Decision & Impact |
| :--- | :---: | :--- | :--- |
| **Kekurangan berat badan (Underweight)** | `< 18,5` (`< 18.5`) | Underweight | Permitted for HR Zone 2 Activity if health screening passes |
| **Berat badan normal (Normal weight)** | `18,5 – 24,9` (`18.5 – 24.9`) | Normal weight | Serves as baseline range for Weight Goal recommendations |
| **Kelebihan berat badan (Overweight)** | `25,0 – 29,9` (`25.0 – 29.9`) | Overweight (Pre-obese) | Target Kurin user; eligible for HR Zone 2 Activity if screening passes |
| **Obesitas (Obesity)** | `≥ 30,0` (`≥ 30.0`) | Obesity | Triggers mandatory assignment to **Easy Walking** track |

---

## 3. Product Rules & Downstream System Integration

### 3.1. Recommended Target Weight Range (Guide 2 §2.4, Step 6)

During the **Weight Goal** onboarding step, the system calculates and displays a recommended target weight range based on the **Normal weight** category ($18.5 - 24.9\text{ kg/m}^2$):

```text
Min Recommended Weight (kg) = 18.5 * (Height (m))^2
Max Recommended Weight (kg) = 24.9 * (Height (m))^2
```

- Example for a user with height 170 cm ($1.70\text{ m}$):
  - $\text{Min Weight} = 18.5 \times 2.89 = 53.465\text{ kg} \approx 53.5\text{ kg}$
  - $\text{Max Weight} = 24.9 \times 2.89 = 71.961\text{ kg} \approx 72.0\text{ kg}$
  - Recommended Range: **53.5 kg – 72.0 kg**

### 3.2. Training Type Assignment (Guide 3 §3.3 & Guide 14 Phase 1)

In the **Final Assessment** step, the user's BMI classification directly dictates training track eligibility:

1. **HR Zone 2 Activity:**
   - Condition: **No health issues based on screening** AND **$\text{BMI} < 30.0$** (Underweight, Normal weight, or Overweight).
   - Frequency options: 3–5 sessions per week.
2. **Easy Walking:**
   - Condition: **Identified health issues from screening** OR **$\text{BMI} \ge 30.0$** (Obesitas / Obesity).
   - Initial plan: Fixed at 3 sessions per week, 15 minutes each.

### 3.3. Numeric Precision & Boundary Policy

- **Computation Precision:** BMI is computed in double-precision floating-point (`Double` in Swift).
- **Comparison Boundary:** The $\ge 30.0$ comparison evaluates unrounded BMI against the threshold $30.0$. A value of exactly $30.0$ belongs to the **Obesitas (Obesity)** category.
- **User Interface Display:** BMI values displayed in profile and goal overview screens are rounded to **1 decimal place** (e.g., `27.4 kg/m²`).
