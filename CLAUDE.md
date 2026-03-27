# CLAUDE.md - Cath Hub

## Project Overview

Cath Hub is a personal life management single-page application (SPA) built as a single `index.html` file (~1400 lines). It covers shopping lists, workout logging, medical tracking, habit/medication tracking, and an integrated AI chat assistant. There is no build system, no framework, and no dependencies.

## Tech Stack

- **Vanilla HTML5, CSS3, JavaScript (ES5+)** — no framework, no transpiler
- **Single file**: everything lives in `index.html` (styles, markup, scripts)
- **No package.json / node_modules / build step** — open `index.html` in a browser
- **Anthropic Claude API**: client-side `fetch` calls to `https://api.anthropic.com/v1/messages` using the `claude-haiku-4-5` model
- **localStorage** for persistence (API key, habits/medication logs)

## Architecture

### Application Structure

All code is wrapped in an IIFE at the bottom of `index.html`. The app container is `<div id="cathHub">` which scopes all CSS and JS.

**Six screens** toggled via sidebar navigation:
| Screen | ID | Description |
|---|---|---|
| Dashboard | `ch-home` | Overview cards linking to other screens |
| AisleMate | `ch-aislemate` | Shopping list manager (weekly lists, family lists, receipts) |
| Workout Log | `ch-workout` | Exercise session builder with pre-built library |
| Medical | `ch-medical` | Medical appointment tracker |
| AI Assistant | `ch-ai` | Full-screen chat with Claude |
| Habits | `ch-habits` | Daily/weekly medication & habit tracker |

A floating AI chat widget is also available on all screens.

### Naming Conventions

Function prefixes map to features:
- `chNav` — top-level navigation
- `am*` — AisleMate (shopping): `amGo`, `amCheck`, `amRefresh`, `amSelectUser`, `amUpdateCounts`
- `wl*` — Workout Log: `wlAddExercise`, `wlRemoveEx`, `wlAddSet`, `wlTickSet`, `wlSetField`, `wlRender`, `wlOpenPicker`, `wlClosePicker`, `wlFilterPicker`
- `hab*` — Habits: `habToggleDaily`, `habToggleWeekly`, `habAddMed`, `habDeleteMed`, `habToggleForm`
- `aiF*` — Floating AI chat: `aiFToggle`, `aiFSaveKey`, `aiFSend`, `aiFKeyDown`
- `ai*` — Full AI tab: `aiSend`, `aiKeyDown`

All public functions are attached to `window`.

### CSS Conventions

- All selectors scoped under `#cathHub` to avoid global conflicts
- `!important` used on all properties for isolation
- BEM-inspired class names: `.am-search`, `.dash-card`, `.shop-item`
- Color palette: slate (#1e293b text), blue (#3b82f6 interactive), green (#10b981 success), amber (#f59e0b highlights)
- Responsive breakpoints: 900px, 768px, 600px
- Sidebar collapses to horizontal bar on mobile (<768px)

### Data & State

- **DOM-based state**: shopping list items, workout session (`WL.session` JS object)
- **localStorage keys**:
  - `cathHub_apiKey` — Anthropic API key
  - `cathHub_logs` — habit/medication log data
  - `cathHub_meds` — medication definitions
- No external database or backend

### Security Notes

- API key is stored in `localStorage` and sent directly from the browser to the Anthropic API
- HTML escaping via `esc()` utility function to prevent XSS in user-generated content
- Never log or expose the API key to third parties

## Development Workflow

### Running Locally

Open `index.html` in any modern browser. No server required (though a local HTTP server avoids CORS issues with the API):

```bash
python3 -m http.server 8000
# or
npx serve .
```

### Making Changes

1. Edit `index.html` directly — CSS is in the `<style>` block, HTML in the `<body>`, JS in the `<script>` block at the bottom
2. Refresh the browser to see changes
3. No linting, formatting, or test infrastructure exists

### File Layout Within index.html

```
Lines 1-3:     DOCTYPE, head, meta
Lines 5-200:   CSS styles (sidebar, dashboard, aislemate, workout, medical, habits, AI, responsive)
Lines 200-690: HTML markup (sidebar nav, all six screens, floating AI widget)
Lines 690+:    JavaScript IIFE (navigation, feature logic, API integration)
```

### Store Tags (AisleMate)

Shopping items can be tagged with store classes:
- `.store-tag.aldi` — blue
- `.store-tag.woolies` — green
- `.store-tag.coles` — red

### Workout Library

Pre-built exercises are defined in the `WL.library` object with two categories: `cardio` and `weights`. Each exercise specifies sets, target, unit, weight, and notes.

## Guidelines for AI Assistants

1. **Single-file app**: all changes go in `index.html`. Do not split into separate files unless explicitly asked.
2. **No build tools**: do not introduce npm, webpack, or similar tooling unless requested.
3. **Preserve scoping**: keep all CSS under `#cathHub` with `!important` flags. Keep all JS functions on `window` inside the IIFE.
4. **Follow naming prefixes**: new functions for a feature should use the established prefix (`am*`, `wl*`, `hab*`, etc.).
5. **HTML escape user input**: use the `esc()` function for any user-provided strings rendered as HTML.
6. **localStorage keys**: prefix with `cathHub_` for any new persistent data.
7. **No external dependencies**: keep the app self-contained. Do not add CDN links or external libraries without explicit approval.
8. **Responsive design**: test changes at mobile (<768px) and desktop widths. Use the existing breakpoint pattern.
9. **Locale**: the app uses `en-AU` locale (Australian English, Australian stores).
