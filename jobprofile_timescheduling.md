# Functional Description – Job Profile and Time Scheduling

---

## Definitions

| Term | Meaning |
|---|---|
| **Job Profile** | A mode that activates work mode for the user, with saved settings for queue login, MBN SMS notification, and display number |
| **Display Number** | The number shown to the caller when the user makes an outgoing call. "My number" refers to the user's fixed, registered primary number in the system |
| **Queue Status** | The combination of logged in/logged out and MBN SMS notification on/off for a given queue |
| **MBN SMS Notification** | A notification service that sends push notifications to the user when incoming SMS messages arrive in a queue |
| **Time Period** | A defined time interval on one or more weekdays with its own settings for queue login and display number |

---

## 1. Job Profile

Job Profile is a mode that can be toggled on and off manually by the user. It saves and restores queue status and display number, allowing the user to quickly enter and exit work mode.

### 1.1 Turning On Job Profile

When Job Profile is turned on:

- Logs the user in to the queues that were active the last time Job Profile was enabled.
- Enables MBN SMS notification for the queues that had it active the last time Job Profile was enabled.
- Sets the display number that was active the last time Job Profile was enabled.
- Does **not** log out of queues the user is already logged in to. For such queues, the existing queue status and MBN SMS notification setting is retained — they are not overridden.

> **First-time use:** If Job Profile has never been activated before and therefore no saved state exists, no queues are logged in to, MBN SMS notification is not enabled, and the display number is not changed. The user must configure the desired setup manually.

### 1.2 Turning Off Job Profile

When Job Profile is turned off:

- Saves the current queue status (logged in/logged out and MBN SMS notification on/off per queue) and the current display number, so that they can be restored the next time Job Profile is turned on. The state is saved regardless of whether it was set manually by the user or automatically by Time Scheduling.
- Logs the user out of all queues.
- Turns off MBN SMS notification for all queues.
- Resets the display number to the user's own number.

### 1.3 Manual Changes While Job Profile Is Active

While Job Profile is active, the user can at any time:

- Log in to and out of individual queues manually.
- Change MBN SMS notification per queue.
- Change the display number.

These changes take effect immediately. If Time Scheduling is active, manual changes apply until the next scheduled event in Time Scheduling, at which point Time Scheduling resumes control.

### 1.4 Visual Status

The app clearly shows whether Job Profile is active or inactive, including the number of queues the user is logged in to.

The status text below the Job Profile toggle varies depending on state:

| Time Scheduling | Job Profile | Status Text |
|---|---|---|
| On | On | 🟢 Time-scheduled: Logged in to n queue(s) |
| On | Off | 🟢 Time-scheduled: Logged out |
| Off | On | 🟢 Logged in to n queue(s) |
| Off | Off | Logged out |
| – | Off (automatic) | ⚫ Logged out automatically |

The last row applies after Time Scheduling has automatically logged the user out. A grey status indicator is shown instead of green, and the text reads "Logged out automatically".

---

## 2. Time Scheduling

Time Scheduling makes it possible to automate Job Profile settings throughout the working day by defining time periods with their own settings per weekday. The Time Scheduling logic runs on the server and is independent of whether the app is active on the device.

### 2.1 Time Periods

- Time periods are configured per weekday (Monday–Sunday).
- Weekdays without defined time periods are inactive — Time Scheduling performs no actions on these days.
- Time periods on the same day cannot overlap. The system must prevent overlapping periods during configuration.

### 2.2 Definition of a Time Period

Each time period has a start time and an end time, and contains the following settings:

#### Display Number
- The selected display number is set at the start of the time period.
- The display number is reset to the user's own number at the end of the time period.

#### Queues
For each queue in the time period, one of the following settings is chosen:

| Setting | Behaviour at Start | Behaviour at End |
|---|---|---|
| **Active** | Logs in to queue and enables MBN SMS notification | Logs out of queue and disables MBN SMS notification |
| **No change** | No action | No action |

- MBN SMS notification for queues set to "Active" is on by default, but can be changed manually by the user during the time period, regardless of the default setting.
- Queues not selected in the time period (i.e. "No change") retain their existing queue status throughout the period — Time Scheduling makes no changes for these.

### 2.3 Job Profile Activation

The user chooses how Time Scheduling activates Job Profile:

| Mode | Behaviour |
|---|---|
| **Automatic** | Job Profile is turned on and off automatically in accordance with the defined time periods |
| **Log in yourself** | The user is notified 10 minutes before a time period starts and ends, and logs in and out themselves via the notification |

#### 2.3.1 Notification Banner

When Time Scheduling executes or announces a scheduled event, an iOS-style notification banner appears at the top of the screen. The banner contains the app name ("Telenor MBN"), a timestamp ("now"), a title, body text, and two action buttons.

**Title** varies depending on the event type:

| Event | Title |
|---|---|
| First time period of the day starts (Job Profile turns on) | "Your Job Profile is turning on at HH:MM" |
| Last time period of the day ends (Job Profile turns off) | "Your Job Profile is turning off at HH:MM" |
| Transition between two time periods mid-day | "Your Job Profile is changing at HH:MM" |

**Body text** describes what will happen:
- Queues being logged in to: "Queue X is being logged in." / "Queues X and Y are being logged in."
- Queues being logged out of: "Queue X is being logged out." / "Queues X and Y are being logged out."
- Display number being set: "Display number is being set to X."
- Display number changing between periods: "Display number is changing to X."
- Display number being reset: "Display number is being reset to your own number."
- When logging out completely: "Display number and queues are being logged out automatically."
- If no specific changes are described, generic text is used.

**Action buttons – "Automatic" mode:**

| Button | Label | Action |
|---|---|---|
| 1 | Postpone 10 min | Cancels automatic execution and dismisses the banner; displays "The change is being postponed 10 minutes" |
| 2 | Do not execute | Cancels automatic execution and dismisses the banner |

If no button is pressed within 8 seconds, the action is executed automatically and the banner is dismissed.

**Action buttons – "Log in yourself" mode:**

| Button | Label | Action |
|---|---|---|
| 1 (primary, bold) | Execute | Executes the action and dismisses the banner |
| 2 | Postpone 10 min | Dismisses the banner without executing; displays "The change is being postponed 10 minutes" |

The banner is automatically dismissed without action after 15 seconds if the user does not interact with it.

### 2.4 Job Profile and Time Scheduling

- Job Profile is turned **on** at the start of the first time period of the day, if it is not already active.
- Job Profile is turned **off** at the end of the last time period of the day, if it is not already inactive.
- This ensures that the user is always logged out of all queues at the end of the working day, and not during any gaps between time periods during the day.
- Time Scheduling **never** logs out queues the user is already logged in to, neither when activating a time period nor when transitioning between periods. Time Scheduling only adds logins for queues configured as "Active" in the period. Queues that are active but not included in the period remain logged in with their existing MBN SMS notification setting unchanged.

### 2.5 Transitions Between Adjacent Time Periods

When two time periods follow directly after each other (end time of period A = start time of period B), the following applies:

- A queue set to "Active" in both periods remains logged in through the transition — the user is not logged out and back in again.
- If the display number differs between the two periods, the display number is changed directly to the next period's number at the period's start — it is not reset to the user's own number in between.
- A queue that is "Active" in period A but "No change" in period B is logged **out** at the end of period A.

### 2.6 Interaction With Manual Changes

Manual changes made by the user during a time period (queue login, MBN SMS notification, display number) apply until the next scheduled event in Time Scheduling, at which point Time Scheduling resumes control.

If the user manually turns off Job Profile while a time period is active, the current time period is ended. The state that was active at the time of the manual turn-off — including any settings applied by Time Scheduling — is saved and will be restored if the user manually turns Job Profile back on. The next scheduled time period will turn Job Profile back on at its start time as normal.

### 2.7 Visual Status for Time Scheduling

The status text below the Time Scheduling toggle always shows "Next:" followed by up to two upcoming scheduled events. The system looks up to 14 days ahead to find the next event.

Each event is formatted as:

> **[Day] at [HH:MM] [Type]**

- **Day**: "Today", "Tomorrow", or the full capitalised weekday name (e.g. "Monday", "Friday")
- **HH:MM**: The scheduled time
- **Type**: See the table below

| Event type | Type label | Description |
|---|---|---|
| First time period of the day starts | On | Job Profile turns on |
| Last time period of the day ends | Off | Job Profile turns off |
| Transition between periods mid-day | Change | Settings update, Job Profile remains on |

When two events are shown, they are stacked vertically and aligned below the "Next:" label.

| State | Status Text |
|---|---|
| Time Scheduling on, 2 or more upcoming events | Next: [event 1] / [event 2] (vertically stacked) |
| Time Scheduling on, exactly one upcoming event | Next: [event] |
| Time Scheduling on, no events in the next 14 days | Next: – |
| Time Scheduling turned off manually | Off |

### 2.8 State on App Launch

When the app opens and Time Scheduling is active (not paused), the app checks whether the current time falls within a configured time period for the current weekday.

**If the current time is within an active period:**
- Job Profile is turned on.
- The period's configured queues are enabled with MBN SMS notification.
- The period's configured display number is set.
- Queues not included in the period are unaffected.

**If the current time is not within any period:**
- The last saved Job Profile state is restored (the same behaviour as manual Job Profile activation).

---

## 3. Configuration – User Interface

### 3.1 Creating a New Time Period

When the user creates a new time period that starts immediately after an existing period:

- The start time defaults to the end time of the previous period.
- Queue settings and display number default to the same values as the previous period, so the user only needs to specify what should change.
- The default end time is start time + 4 hours.

When the user creates a time period without an adjacent existing period:

- Start time and end time are set to default values (e.g. 08:00–16:00).
- All queues are set to "No change" by default.

### 3.2 Validation

- The system prevents overlapping time periods on the same day.
- The end time must be later than the start time.
- The user is notified with a clear error message if validation fails.

### 3.3 Onboarding – First-Time Setup of Time Scheduling

The first time the user activates Time Scheduling, they go through a wizard with the following steps:

1. **Working hours** – Select start time, end time, and weekdays for the work session.
2. **Display number** – Select which number should be shown during the work session.
3. **Queues** – Select which queues should be active during the work session. MBN SMS notification is automatically enabled for selected queues and can be changed manually afterwards. At the end of the time period, selected queues are logged out, MBN SMS notification is turned off, and the display number is reset. Queues that are not selected will be unaffected.
4. **Job Profile activation** – Choose between "Automatic" (logged in and out without any action required) and "Log in yourself" (notified 10 minutes before the period starts and ends). Notifications can be turned off under settings afterwards.

Once the wizard is complete, Time Scheduling is active and configured. The user can change all settings via "Edit time scheduling".
