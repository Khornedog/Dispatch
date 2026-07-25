# Dispatch — Work Queue Console

Dispatch is a single-file web app for managing a personal work portfolio: log tasks with priority, difficulty, and time estimates, and let the app suggest what to work on next based on a scoring system — or just ask it in plain language.

**Current version: v1.6.3** (shown in the app header; see [Version History](#version-history) below)

## Features

- **Task tracking** — title, notes, priority, difficulty, estimated time, due date, tags.
- **Priority scale** — five named levels instead of raw numbers: **Top, High, Medium, Low, Whatevs** (Top = highest). Only one task can be Top at a time; setting a second Top prompts you to confirm the swap.
- **Difficulty gauge** — a half-moon dial (Easy → Hard) instead of a plain slider, color-coded on the same scale as priority.
- **Automatic scoring & "Next Up"** — every task gets a numeric score from priority, difficulty, time, and due-date urgency; the top-scoring task is surfaced as a recommendation, with a "Reroll" option.
- **Status actions**:
  - **Done** — closes the task (can be manually reactivated later from the Closed section if needed).
  - **Done for Day** — removes it from today's queue; it reactivates automatically the next calendar day.
  - **Hold…** — set it aside until a specific day, or a quick duration (15 min / 1 hr / 4 hr / tomorrow 9am); it reactivates automatically once the hold expires.
  - **De-prioritize** — sets it aside indefinitely until manually reactivated.
- **Recurring tasks**:
  - **Daily** — reactivates the next day after being marked Done.
  - **Weekly** — pick one or more days of the week; reactivates on the next occurrence of any selected day after being marked Done.
- **Chat assistant ("Ask the Queue")** — describe how much time or energy you have, and it recommends the best-fitting active task, using the Anthropic API. Supports voice input (browser speech recognition) and optional spoken replies.
- **Export / Import** — download your task list as a `.json` file (also copied to clipboard) and import it elsewhere, merging in anything new without touching existing tasks.

## Getting Started

### Option A: Run it inside Claude (recommended)

Open `dispatch.html` as a Claude artifact (e.g. by uploading it into a claude.ai conversation, or continuing to use the version already shared with you there). This is the only mode where:
- Task and chat data **persist automatically** and can sync across devices logged into the same Claude account.
- The **chat assistant** works, since it calls the Anthropic API using Claude's built-in credentials — no API key setup needed.

### Option B: Run it standalone in any browser

Just open `dispatch.html` directly (double-click it, or drag it into a browser tab). In this mode:
- Task and chat data are saved to that browser's **`localStorage`** automatically — persisting across sessions on that device/browser, but **not syncing** to other devices or browsers.
- The **chat assistant will not work** — it calls `https://api.anthropic.com/v1/messages` directly from the page with no API key attached, which only succeeds inside Claude's own environment. Everything else (task tracking, scoring, hold/daily/weekly, export/import, voice input for dictation) works fully offline.

### Moving data between copies

Use the **Export** button to download a `.json` snapshot of your tasks (or copy it from your clipboard), then **Import** it into another copy of the app — on another device, another browser, or after switching between the Claude and standalone versions. Import merges by task ID; it won't duplicate or overwrite existing tasks.

## How Scoring Works

```
score = (6 − priority) × 22 − difficulty × 6 − min(estMinutes, 240) / 240 × 20 + bonus
```

- **Priority** dominates: Top contributes +110, Whatevs only +22.
- **Difficulty** and **estimated time** apply small penalties, favoring easier/shorter tasks as a tiebreaker.
- **Bonus**: +45 if overdue, +32 if due today/tomorrow, +16 if due within 3 days, +6 if due within a week, or a flat **+33** if the task is marked Daily or Weekly (edging out same-priority tasks due today/tomorrow).

## Version History

| Version | Changes |
|---|---|
| v1.0.0 | Initial build: task tracking, scoring, hold/park states, chat assistant, voice input |
| v1.1.0 | "Top" priority exclusivity rule |
| v1.2.0 | Priority renamed to named levels; scoring direction reversed (1 = highest) |
| v1.3.0 | Vertical priority buttons + difficulty gauge, color-coded |
| v1.4.0 | Daily recurrence |
| v1.4.1 | Weekly recurrence (single day) |
| v1.4.2 | Weekly recurrence: multi-day select |
| v1.5.0 | Export/Import; environment-aware storage (Claude storage with localStorage fallback) |
| v1.6.0 | Local-timezone day boundaries; day-only Hold option; version display added |
| v1.6.1 | Added "Reactivate" button for closed (Done) tasks |
| v1.6.2 | Task add/edit modal no longer closes when clicking outside it |
| v1.6.3 | Voice input errors now surface a message instead of failing silently |

## Notes & Limitations

- This is a single self-contained HTML file — no build step, no dependencies to install.
- Voice input relies on the browser's built-in speech recognition (best support in Chrome/Edge; limited in Firefox/Safari). When run inside Claude's artifact preview, the sandboxed environment may block microphone access entirely — if voice input doesn't work there, try the standalone downloaded file in a regular browser tab instead.
- There's no encryption or account system — anyone with access to the file/browser profile can see the stored tasks.
