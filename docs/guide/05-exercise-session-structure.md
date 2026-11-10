# 5. Exercise Session Structure

Each exercise session is divided into three primary phases applicable to both Easy Walking and HR Zone 2 Activity.

### 5.1. Warm-up

The warm-up phase prepares the body before entering the main workout.

- Activity: Easy Walking.
- Minimum duration: 5 minutes.
- Maximum duration: 10 minutes.

### 5.2. Actual Training

The main workout phase follows the training type specified in the user's Training Plan.

- Easy Walking for users on the Easy Walking track.
- HR Zone 2 Activity for users on the HR Zone 2 Activity track.

The duration of this main phase corresponds to the total session duration minus warm-up and cool-down durations.

### 5.3. Cool-down

The cool-down phase promotes active recovery after the main workout concludes.

- Activity: Easy Walking.
- Minimum duration: 5 minutes.
- Maximum duration: 10 minutes.

### 5.4. Session Duration Calculation

Variables used:

- `D`: Total workout duration per session in minutes.
- `F`: Training frequency per week.
- `W`: Duration of warm-up and cool-down, each in minutes.
- `Z`: Duration of the main workout phase (Actual Training) in minutes.

Warm-up and cool-down duration formula:

```text
W = min(10, 5 + floor((D - 15) / 15))
```

Main workout phase duration formula:

```text
Z = D - (2 * W)
```

Total weekly duration formula:

```text
Weekly Duration = D * F
```

Total weekly actual training duration formula:

```text
Weekly Actual Training = Z * F
```

### 5.5. Calculation Example

For a session duration of 50 minutes (`D = 50`) and a frequency of 3 times per week (`F = 3`):

| Phase | Duration |
|---|---:|
| Warm-up | 7 minutes |
| Actual Training | 36 minutes |
| Cool-down | 7 minutes |
| **Total per session** | **50 minutes** |

Total weekly duration: **150 minutes**.

Total weekly actual training duration: **108 minutes**.
