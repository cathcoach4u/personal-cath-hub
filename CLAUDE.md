# Cath Hub

Personal health and lifestyle management app for shopping, meal planning, workout tracking, medication management, mental health monitoring, and AI-assisted personal assistance.

## Stack

- **Frontend:** Vanilla JavaScript + CSS in a single `index.html` file (~3,165 lines)
- **Database:** Supabase (PostgreSQL) with real-time subscriptions
- **AI:** Claude Haiku 4.5 via Supabase Edge Function proxy (`/functions/v1/claude-proxy`)
- **Auth:** Supabase email/password authentication

## Project Structure

- `index.html` — Entire SPA (HTML + CSS + JS)
- `items.json` — Master shopping items database
- No build step — serve directly via any HTTP server

## Running Locally

```bash
npx http-server
```

## Coding Conventions

### JavaScript
- Prefix naming by feature domain:
  - `am*` = AisleMate (shopping)
  - `wl*` = Workout Log
  - `hab*` = Habits
  - `af*` = AI Float (assistant)
  - `med*` = Medical
  - `ch*` = Core navigation/app
- Async/await for all Supabase calls
- DOM manipulation via `getElementById`, `querySelector`, `innerHTML`
- Event handling via inline `onclick` and event listeners

### CSS
- All rules scoped under `#cathHub` or `#cathHub-login`
- Uses `!important` throughout to prevent conflicts
- Organized by feature section with comment headers (e.g., `/* ───── LOGIN SCREEN ───── */`)
- Color palette: Blues (#3b82f6), Greens (#10b981), Slate grays (#1e293b, #e2e8f0)

### UI Patterns
- Tab switching via `chNav()` with active state management
- Modal-style overlaid forms
- Emoji unicode for icons
- Mobile-first responsive design

## Key Supabase Tables

| Table | Purpose |
|-------|---------|
| `shopping_items` | Active shopping list |
| `master_items` | Item catalog with pricing history |
| `receipts` / `receipt_items` | Receipt metadata and line items |
| `medications` | Medication definitions |
| `habit_meds` | Medication habit tracking |
| `habit_logs` | Custom habit completion logs |
| `medical_sessions` | Mental health session records |
| `meal_plans` | Weekly meal planning |
| `ai_memory` | Persistent AI conversation memory |

## Important Notes

- This is a single-file SPA — all changes go in `index.html`
- No test suite or linter configured
- API keys are stored server-side in Supabase Secrets, not in frontend code
- The app is scoped to `#cathHub` container to prevent CSS conflicts
