### Date Machine Documentation

#### Overview
The Date Machine is a finite state machine designed to iterate through dates within a specified range and collect all occurrences of a target day of the week. It leverages the Temporal API for reliable date calculations and state transitions to efficiently navigate dates.

---

### Core Components

#### 1. **Type Definitions**
- **`DateMachineStates`**:  
  Represents possible states of the state machine:
  - `"entry"`: Initial state to check if the current date matches the target day.
  - `"checkNextDay"`: Computes the next occurrence of the target day.
  - `"-1"`, `"0"`, `"1"`: Handle date comparisons against the range end.
  - `"end"`: Terminal state.

- **`DateMachineContext`**:  
  Context object passed through state transitions:
  ```typescript
  {
    currentDate: Temporal.PlainDate,      // Current date being processed
    targetDayOfWeek: number,              // Target day (1-7, Mon-Sun)
    dateRange: { from, to },              // Date boundaries (inclusive)
    dateStack: Temporal.PlainDate[],      // Accumulated matching dates
    nextDate?: Temporal.PlainDate         // Computed next candidate date
  }
  ```

#### 2. **Helper Functions**
- **`getDayOffset(startDay, targetDay)`**:  
  Calculates the offset (in days) from `startDay` to `targetDay`.  
  **Formula**: `targetDay - startDay`  
  **Throws**: If inputs are `undefined`.

- **`getNextDayOffset(currentDay, targetDay)`**:  
  Calculates days needed to reach the **next** `targetDay` after `currentDay`.  
  **Formula**: `(7 - currentDay) + targetDay`  
  **Throws**: If inputs are `undefined`.

#### 3. **State Machine: `DateMachine`**
Manages state transitions and date processing logic.

- **States**:
  | State          | Action                                                                 | Next State       |
  |----------------|------------------------------------------------------------------------|------------------|
  | `"entry"`      | Checks if `currentDate` is the target day. Pushes to `dateStack` if yes. | `"checkNextDay"` |
  | `"checkNextDay"` | Computes `nextDate` (next target day). Compares it against `dateRange.to`. | `"-1"`, `"0"`, or `"1"` |
  | `"-1"` or `"0"` | Pushes valid `nextDate` to `dateStack`, updates `currentDate = nextDate`. | `"checkNextDay"` |
  | `"1"`          | Terminates the machine.                                               | `"end"`          |

- **Methods**:
  - `transition(newState)`: Updates the current state.
  - `exec(context)`: Executes logic for the current state, returns next state.

#### 4. **Main Function: `getEveryOtherWeekDay(dateRange, dayOfWeek)`**
Finds all occurrences of `dayOfWeek` (1-7) within `dateRange`.

**Steps**:
1. Initialize the state machine in `"entry"` state.
2. Set up context with:
   - `currentDate = dateRange.from`
   - `targetDayOfWeek = dayOfWeek`
   - Empty `dateStack`
3. Run the machine until state becomes `"end"`.
4. Return `dateStack` (collected dates).

---

### Workflow Example
**Goal**: Find all Mondays (`dayOfWeek = 1`) from **2025-08-18** (Mon) to **2025-08-31** (Sun).

1. **`entry` State**:
   - `currentDate = 2025-08-18` (Monday)
   - `offset = getDayOffset(1, 1) = 0` → Push to `dateStack`.
   - Transition to `"checkNextDay"`.

2. **`checkNextDay` State**:
   - Compute next Monday:  
     `nextDate = 2025-08-18 + getNextDayOffset(1, 1) = 2025-08-25`.
   - Compare `2025-08-25` (nextDate) vs. `2025-08-31` (range end):  
     `Temporal.compare(nextDate, to) = -1` → Transition to `"-1"`.

3. **`"-1"` State**:
   - Push `2025-08-25` to `dateStack`.
   - Update `currentDate = 2025-08-25`.
   - Transition to `"checkNextDay"`.

4. **`checkNextDay` Again**:
   - Next Monday: `2025-09-01`.
   - Compare `2025-09-01` vs. `2025-08-31`: Result `1` → Transition to `"1"`.

5. **Termination**:
   - State `"1"` → `"end"`. Return `dateStack = [2025-08-18, 2025-08-25]`.

---

### Key Design Notes
1. **Efficiency**:
   - Skips non-relevant dates by directly calculating offsets to the next target day.
   - Avoids brute-force iteration through all dates in the range.

2. **Temporal API**:
   - Uses `Temporal.PlainDate` for immutable, timezone-agnostic date operations.
   - Methods like `.add()`, `.dayOfWeek`, and `compare()` ensure correctness.

3. **Error Handling**:
   - Throws explicit errors for undefined inputs in helper functions.
   - Guards against invalid states (e.g., `nextDate` used before computation).

4. **State Encapsulation**:
   - Each state has a single responsibility, making logic easy to extend/debug.

---

### Usage
```javascript
import getEveryOtherWeekDay from './dateMachine';
import { Temporal } from '@js-temporal/polyfill';

// Define date range (inclusive)
const dateRange = {
  from: Temporal.PlainDate.from('2025-08-01'), // Start date
  to: Temporal.PlainDate.from('2025-08-31')    // End date
};

// Get all Fridays (day 5)
const fridays = getEveryOtherWeekDay(dateRange, 5);
console.log(fridays.map(d => d.toString()));
// Output: ['2025-08-01', '2025-08-08', '2025-08-15', '2025-08-22', '2025-08-29']
```

---

### Edge Cases
- **Start/End on Target Day**: Both dates are included if they match.
- **Single-Day Range**: Returns `[startDate]` only if it matches the target day.
- **No Matching Dates**: Returns an empty array (e.g., target day not in range).

---

### Dependencies
- **`@js-temporal/polyfill`**: Required for Temporal API support in environments without native implementation.
