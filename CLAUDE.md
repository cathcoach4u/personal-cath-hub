# CLAUDE.md - Cath Hub

## Project Overview

Cath Hub is a personal health & household management PWA built with vanilla HTML/CSS/JS (no frameworks), Supabase backend, hosted on GitHub Pages. Repository: cathcoach4u/personal-cath-hub.

**Timezone:** Australia/Sydney (AEST/AEDT). Always use Sydney time.

## Architecture

- Single-page app: `index.html` contains all screens with sidebar navigation
- Standalone utility pages: `morning.html`, `todo.html`, `workout.html`, `ai.html`
- ARCHIVED: `shopping.html`, `fiona.html`, and AisleMate screen (moved to Baker Hub)
- Login via Supabase Auth (email/password) with optional PIN lock
- Navigation: `chNav(screenName, btn)` function switches screens
- All CSS is inline in `<style>` tags, scoped with `#cathHub` prefix and `!important`
- Dark mode toggle available
- No build step required - pure HTML/CSS/JS
- Deploy via GitHub Pages

## Screens (in index.html)

1. **Dashboard** (`ch-home`) - overview cards with daily habits, rhythm checklist, fortnightly appointments, meds button, water tracker, to-do summary
2. **To-Do** (`ch-todo`) - personal tasks with categories and due dates
3. **AisleMate** (`ch-aislemate`) - ARCHIVED (moved to Baker Hub)
4. **Workout Log** (`ch-workout`) - exercise tracking with sets/reps, bottom-sheet picker
5. **Medical** (`ch-medical`) - medications, sessions, contacts, mental health
6. **My Habits** (`ch-habits`) - medication tracking (daily/weekly/as-needed), custom habits, rhythm cards, zone buddy
7. **Travel** (`ch-travel`) - travel planning
8. **Home** (`ch-hometab`) - household management
9. **About** (`ch-about`) - app info, built-with, stack, features, Supabase tables

## Standalone Pages

- `morning.html` - Morning medication auto-recording (Vyvanse, Valtrex, Levothyroxine)
- `todo.html` - Personal to-do list with categories and due dates
- `workout.html` - Workout logging with exercise library
- `ai.html` - Standalone AI assistant (Cath AI) with tool calling

## Supabase Tables

- `personal_todos` (id, task, done, category, due_date, created_at) - id must be explicitly set with `Date.now()`
- `habit_meds` (id, name, freq ['daily', 'weekly', 'asneeded'])
- `habit_logs` (id, med_id, date, created_at, note)
- `medications` (name, dose, schedule, purpose)
- `medical_sessions` (date, duration, plan, practitioner)
- `master_items` (name, category, store, family_member, last_price)
- `receipts` (store, receipt_date, total, uploaded_at)
- `receipt_items` (item_name, price, receipt_id)
- `meal_plans` (day_index, meal data)
- `ai_memory` (fact, created_at)
- `shopping_items` (name, category, store, checked, created_at) - ARCHIVED
- `fiona_tasks` (task, done, created_at) - ARCHIVED
- Storage bucket: `receipts` (for receipt images/PDFs)

## Medication System

### Structure (My Habits > Medications tab)

**Daily — Morning**: Vyvanse, Valtrex, Levothyroxine. Checkbox per med, streaks, time taken, notes.

**Daily — Ointments** (sequential use):
- Xobet Cream: once daily evening, use until tube runs out (tracked as daily checkbox)
- Betamethasone Valerate Ointment: up to 3x daily, activates after Xobet is marked finished
- Toggle via `localStorage` key `cathhub_xobet_finished`
- `OINTMENT_MEDS` array in index.html defines the sequence
- `OINTMENT_NAMES` array used to exclude ointments from morning meds logic

**Weekly**: Vitamin D, Monjaro. Checkbox shows "Taken this week" / "Not yet taken this week".

**As Needed**: Nurofen, Panadol, Nexium Antacid, Prochlorperazine, Cizinate, Ondansetron. Each dose recorded with time, notes, brand picker (Nexium). `AS_NEEDED_MEDS` array defines config.

### Key Arrays (index.html)
- `MED_TIMES` - time-of-day labels for daily meds (Morning)
- `OINTMENT_MEDS` - sequential ointment definitions with name, dose, time, purpose
- `OINTMENT_NAMES` - lowercase names for filtering ointments from morning-only logic
- `AS_NEEDED_MEDS` - as-needed med definitions with dose, group, purpose, hasBrands
- `dailyMedNames` - names auto-populated as daily freq in habit_meds
- `weeklyMedNames` - names auto-populated as weekly freq in habit_meds

### Dashboard Meds Button
- "Take Morning Meds" / "All Meds Taken" button only covers morning meds (excludes ointments via `OINTMENT_NAMES` filter)
- `dashTakeMorningMeds()`, `dashUpdateMedsBtn()`, `dashCheckMedReminder()`, `dashBackfillMeds()` all filter out ointments

### Auto-populate (`habAutoPopulate`)
- Syncs medications table to habit_meds with correct freq
- Fixes wrong frequencies for weekly meds and ointments
- Cleans up duplicates and daily log duplicates

## Dashboard Features

### Fortnightly Week A/B
- Reference: Week A starts 16 March 2026
- `zbGetFortnightWeek()` / `dashGetCurrentWeek()` calculate current week
- Week A: Massage — Jo Jo (fortnightly session)
- Week B: Chiro — Daniel (fortnightly session)
- Clickable cards with done state stored in localStorage per fortnight week
- "Swap" button to preview other week

### Daily Rhythm Checklist
- Time-based sections: Morning (before noon), Through the Day (noon-5pm), Evening (after 5pm)
- Checkboxes stored in localStorage per day

### Water Tracker
- 8-glass target, stored in localStorage per day

## AI Assistant

### Standalone (`ai.html`)
- Uses Anthropic API with tool calling
- Suggestion chips at bottom: Open todos, Meds today, Backfill meds, Overdue tasks, Memories, Add a to do
- System prompt includes full medication list and instructions to ask about new meds

### Embedded (index.html floating panel)
- Same tool set as standalone, embedded in bottom-right chat panel
- System prompt variable: `afSystemPrompt`

### AI Tool Notes
- `add_todo`: must include `id: Date.now()` and `created_at: new Date().toISOString()`
- `add_fiona_task`: uses `id: Date.now()`
- Shopping/Fiona tools: ARCHIVED, AI instructed to redirect to Baker Hub
- Ointments: AI says "applied" not "taken" for topical meds

## Design System

### Colors

- Top bar / sidebar / mobile nav: teal `#0d9488`
- Active nav highlight: `rgba(59,130,246,0.3)` with `#3b82f6` border
- Background: `#f8fafc`
- Cards: white with `#e2e8f0` border, 10px radius
- Text: `#1e293b` (headings), `#64748b` (labels), `#94a3b8` (meta)
- Success/done: `#10b981` (green)
- Warning: `#f59e0b` (amber)
- Action: `#3b82f6` (blue)
- Purple accent: `#7c3aed`

### Typography

- Font: `'Segoe UI', system-ui, -apple-system, sans-serif`
- Headings: 22px/700, Section labels: 14px/700, Body: 12-13px

### Layout

- Desktop: 240px teal sidebar + white content area
- Mobile (under 768px): Sticky teal top bar with hamburger dropdown menu
- Cards: white, 1px border `#e2e8f0`, 10px radius, 16px padding
- Buttons: teal/dark background, white text, 7px radius
- Badges/tags: 9-10px font, pill shape, colour-coded backgrounds
- Tables: 10px uppercase headers, 12px body, hover highlight `#f8fafc`

### Component Patterns
- Done state card: green background `#f0fdf4`, green border `#bbf7d0`, checkmark icon, "Done" subtitle
- Pending state card: white background, grey border `#e2e8f0`, chevron arrow
- Sequential items: active item at full opacity with "CURRENT" badge, inactive at `opacity:0.5` with "NEXT" badge

### PWA

- `manifest.json` with standalone display, teal theme, SVG icons with white "C"

## Development Notes

- All styling uses `!important` due to `#cathHub` scope reset
- Supabase client initialized with public anon key
- No build step required - pure HTML/CSS/JS
- Deploy via GitHub Pages
- `personal_todos` table requires explicit `id` (no auto-increment) - always use `id: Date.now()`
- `habit_logs` table has optional `note` column (text, nullable)
- localStorage keys used: `cathhub_xobet_finished`, `cathhub_antacid_brands`, `cathHub_missedDismissed_*`, `fn_done_week_*`, `rhythm_*`, `water_*`
