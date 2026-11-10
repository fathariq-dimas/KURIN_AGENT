# 6. Exercise Guidance System

Exercise Guidance is a guidance system designed to help users execute workout sessions according to plan.

The system utilizes audio prompts and haptic feedback throughout the workout.

Guidance is divided into two categories: General Exercise Guidance and HR Zone 2 Guidance.

### 6.1. General Exercise Guidance

General Exercise Guidance applies to all training types.

Prompts are delivered at each phase transition and upon session completion.

**Session Start**

The system provides audio guidance when the session begins and the user enters the Warm-up phase.

**Actual Training Start**

Once the Warm-up duration is completed, the system delivers audio guidance announcing that the user is transitioning into the main workout phase.

**Cool-down Start**

Upon completing the Actual Training duration, the system delivers audio guidance to initiate the Cool-down phase.

**Session Complete**

When all exercise phases are concluded, the system delivers audio guidance indicating that the targeted session duration has been achieved.

### 6.2. HR Zone 2 Guidance

HR Zone 2 Guidance is an additional guidance layer dedicated exclusively to users on the HR Zone 2 Activity track.

The system continuously monitors the user's heart rate during the Actual Training phase and provides real-time cues based on heart rate positioning relative to the Zone 2 target range.

**Speed Up**

- **Condition:** Heart rate falls below the lower threshold of Zone 2.
- **Guidance:** The system prompts the user to gradually increase activity pace to enter the Zone 2 range.

**Maintain**

- **Condition:** Heart rate is within the Zone 2 target range.
- **Guidance:** The system prompts the user to maintain their current pace and intensity.

**Slow Down**

- **Condition:** Heart rate exceeds the upper threshold of Zone 2.
- **Guidance:** The system prompts the user to reduce activity pace to bring heart rate back down into Zone 2.

**Take Your Time**

- **Condition:** Heart rate remains elevated above Zone 2 despite the user slowing down.
- **Guidance:** The system instructs the user to temporarily pause activity and take time to recover.

Once the heart rate drops below the prescribed upper threshold for resumption and the user feels ready, the system guides them to restart activity gently to return to Zone 2.

This guidance empowers users to take active pauses without feeling pressured to stay in continuous motion to maintain target intensities.

### 6.3. Guidance by Training Type

| Guidance Cue | Easy Walking | HR Zone 2 Activity |
|---|---|---|
| Session Start | Yes | Yes |
| Actual Training Start | Yes | Yes |
| Cool-down Start | Yes | Yes |
| Session Complete | Yes | Yes |
| Speed Up | No | Yes |
| Maintain HR Zone | No | Yes |
| Slow Down | No | Yes |
| Take Your Time | No (not as a Zone 2 cue) | Yes |
