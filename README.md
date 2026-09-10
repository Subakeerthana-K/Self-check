# Self-Check

**Plan less. Do more. Stay on track.**

Self-Check is a minimal, professional personal task and daily-assignment management web app. It combines a focused task dashboard, a lightweight AI productivity assistant ("CheckMate"), and a small set of motivational systems (on-time streaks, email reminders) — all wrapped in a calm, distraction-free interface with full light/dark theming.

This repository contains a **self-contained front-end prototype**: a single HTML file with no build step, no external dependencies beyond a Google Font, and no server required.

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Getting Started](#getting-started)
- [Project Structure](#project-structure)
- [Design System](#design-system)
- [Core User Flows](#core-user-flows)
- [Data & Persistence](#data--persistence)
- [Known Limitations](#known-limitations)
- [Roadmap to Production](#roadmap-to-production)
- [Customization Guide](#customization-guide)
- [Browser Support](#browser-support)
- [License](#license)

---

## Overview

Self-Check is built around one core loop:

```
Login → Dashboard → Add Task → Set Deadline → Work → Reminder → Complete → Progress Updated
```

The interface is intentionally restrained — one clear primary action per screen, generous whitespace, a single accent color, and no unnecessary configuration. It is designed to feel like a personal productivity tool, not a team project-management suite.

---

## Features

### Authentication
- Centered login screen with email/password fields and password visibility toggle
- Client-side email format validation with inline error state (invalid emails are blocked from entry)
- Display name is automatically derived from the entered email address (e.g. `sarah.jones@mail.com` → **Sarah Jones**) and used throughout the app (greeting, avatar initials, profile panel)
- "Continue with Google" and "Create account" entry points (simulated for this prototype)

### Dashboard
- Daily summary card: total tasks, completed count, tasks due today, and a real-time **on-time completion streak**
- Two-column board — **To Do** and **Completed** — with full drag-and-drop support
- Mobile-friendly checkbox-based completion as an alternative to dragging
- Checkmark-pop and particle-burst micro-animation on task completion
- Empty-state screen for new users with zero tasks

### Task Management
- Create, edit, and delete tasks via a compact modal
- Fields: title, description, category (Assignment / Study / Personal / Project / Other), due date & time, priority (Low / Medium / High), and reminder lead time
- Task detail view with edit / complete / delete actions
- Clear deadline states: normal, due today, due soon, and overdue

### Dedicated Views
- **Today** — a grouped Morning / Afternoon / Evening daily planner
- **Assignments** — filterable list (All / Upcoming / Due Soon / Completed) for academic work
- **Completed** — historical log of finished tasks, filterable by Today / This Week / This Month

### CheckMate AI Assistant
- Floating assistant panel with quick actions: *Plan my day*, *Show urgent tasks*, *Break down a task*, *Create a task*
- Lightweight natural-language task creation (parses relative dates like "tomorrow," times like "7 PM," and duration/time-of-day phrases like "evening")
- Task breakdown into a generic subtask checklist, addable in one click
- Priority-aware daily planning suggestions

### On-Time Streak System
- Completing a task **before its deadline** increases the streak
- Completing a task **late**, or letting a deadline pass without completing it, resets the streak to zero
- Streak is checked continuously in the background (every 30 seconds) while the app is open
- Current streak persists across sessions

### Email Reminders (Simulated)
- Configurable reminder lead time per task: 5 / 15 / 30 minutes, 1 hour, or none
- When a task enters its reminder window, a simulated reminder "email" fires — surfaced as an in-app toast and logged in the notification panel
- Reminder logic is fully implemented and timer-driven; see [Known Limitations](#known-limitations) for what's required to send a real email

### Notifications
- Bell icon with unread badge, showing due-soon tasks, overdue tasks, recently sent reminders, and the most recently completed task

### Appearance
- Light, Dark, and **System Default** theme modes
- Theme preference persists across sessions
- Live-updates if the OS theme changes while "System Default" is active

### Settings
- Profile (name, email)
- Notification toggles (email reminders, due-soon alerts, overdue alerts)
- CheckMate AI enable/disable
- Theme selection
- Account actions (change password, log out)

### Responsive Design
- Desktop: collapsible left sidebar
- Tablet: condensed sidebar
- Mobile: bottom tab navigation with a centered "Add Task" action

### Accessibility
- Keyboard-navigable controls and visible focus states
- Non-drag alternative for every drag-and-drop action
- Status is never communicated by color alone (icons/text accompany all state indicators)

---

## Getting Started

No installation or build step is required.

1. Download `self-check.html`
2. Open it directly in any modern browser (double-click, or `File → Open`)
3. Sign in with any correctly formatted email address (password is not validated in this prototype)

That's it — the entire application runs client-side in a single file.

---

## Project Structure

```
self-check.html      # Entire application: markup, styles, and logic in one file
README.md            # This document
```

All CSS lives in a single `<style>` block and all JavaScript in a single `<script>` block at the bottom of the file, organized into clearly commented sections:

```
STATE            → in-memory application state (tasks, user, settings, streak)
STORAGE          → theme & streak persistence
THEME            → light / dark / system theme engine
HELPERS          → date/time formatting, due-status logic
RENDER           → dashboard, today, assignments, completed, notifications
TASK MODAL       → create / edit task form
DETAIL MODAL     → task detail view
NAVIGATION       → view switching, sidebar, mobile nav
STREAK / DEADLINE WATCH   → reminder + missed-deadline timers
CHECKMATE AI     → assistant panel and natural-language parsing
LOGIN FLOW       → validation, name derivation, session start
```

---

## Design System

| Token | Purpose |
|---|---|
| `--bg`, `--card`, `--border` | Base surface colors (light/dark variants) |
| `--text`, `--text-2`, `--text-3` | Primary, secondary, and tertiary text |
| `--accent` | Primary brand color — used for CTAs, completed states, streak, and the AI assistant |
| `--warn` / `--danger` | Due-soon and overdue states |
| `--shadow-sm` / `--shadow-md` / `--shadow-lift` | Elevation scale for cards, dropdowns, and modals |

Typography uses **Manrope**, loaded from Google Fonts, with a clear weight-based hierarchy (800/700 for headings, 600 for labels, 400–500 for body text).

Motion follows a consistent easing curve (`--ease-out: cubic-bezier(0.16, 1, 0.3, 1)`) applied to entrances, hovers, and state changes throughout the app, so animation feels cohesive rather than decorative.

---

## Core User Flows

**Completing a task**
```
Click checkbox → checkmark pop + particle burst → card animates out
→ streak evaluated (on-time vs. late) → toast confirmation → card appears in Completed
```

**Getting help from CheckMate**
```
"Plan my day" → pending tasks sorted by priority & deadline → proposed schedule → "Add schedule"
```

**Missing a deadline**
```
Task passes due time while still pending → detected on next 30s check
→ streak resets to 0 → toast notification
```

---

## Data & Persistence

This prototype uses the browser's **Storage API** (`window.storage`) to persist two pieces of state across sessions:

- Selected theme mode (`light` / `dark` / `system`)
- Current and best on-time streak

All other data — tasks, user profile, notification log, settings — lives in memory for the duration of the session and resets on page reload. This is intentional for a front-end prototype; see below for how to make it durable.

---

## Known Limitations

This build is a **front-end-only prototype** intended to demonstrate the full product experience, interaction design, and motion language. A few features are simulated rather than fully wired to backend infrastructure:

| Feature | Current Behavior | What's Needed for Production |
|---|---|---|
| Email reminders | Simulated in-app (toast + notification log) | Backend scheduler + transactional email service (e.g., Resend) |
| Authentication | Any valid-format email signs in; password unchecked | Real auth provider (e.g., Supabase Auth) with Google OAuth |
| Task storage | In-memory, resets on reload | Persistent database (e.g., Supabase Postgres) |
| CheckMate AI | Rule-based parsing and canned responses | LLM-backed assistant with real task context |
| Streak / deadline checks | Client-side timer (only runs while the tab is open) | Server-side scheduled job, independent of client sessions |

The reminder and streak **trigger logic** (when to fire, how to detect a missed deadline, on-time vs. late evaluation) is fully implemented and ready to be moved server-side — only the delivery mechanism (email, persistent cron) needs to be added.

---

## Roadmap to Production

Recommended architecture for turning this prototype into a full product, per the original product spec:

- **Frontend:** React + TypeScript, componentized from the current single-file structure
- **Styling:** Tailwind CSS, using the existing design tokens as a starting palette
- **Auth & Database:** Supabase (email + Google authentication, task/user storage)
- **Email:** Resend, triggered by a scheduled backend job evaluating reminder windows
- **AI:** A lightweight agent with read/write access to the user's task data, replacing the current rule-based parser

---

## Customization Guide

- **Brand color:** update `--accent` (and its `-soft` / `-ink` variants) in both the `[data-theme="light"]` and `[data-theme="dark"]` blocks at the top of the stylesheet
- **Default reminder lead time:** change the default `<option selected>` in the task modal's reminder `<select>`, and the fallback value set in `openTaskModal()`
- **Streak check frequency:** adjust the interval passed to `setInterval(checkMissedDeadlines, 30000)`
- **Seed/demo data:** edit the `tasks` array in the `state` object near the top of the script

---

## Browser Support

Tested in current versions of Chrome, Edge, Firefox, and Safari. Uses standard CSS custom properties, CSS Grid/Flexbox, the native HTML5 Drag and Drop API, and `prefers-color-scheme` — no polyfills included.

---

