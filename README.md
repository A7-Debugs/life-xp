# LIFE XP

A gamified personal-development and GCSE revision app for a 14-year-old: an RPG progression
system, Duolingo-style quests and a fitness tracker in one installable, offline-first Android PWA.

**Live app:** https://a7-debugs.github.io/life-xp/

LIFE XP is a standalone project with its own database (`life-xp` in IndexedDB), icons and service
worker. It shares no code, data or settings with any other app.

## Run it

```bash
npm install
npm run dev        # http://localhost:5173 (for development)
npm run build      # production build → dist/
npm run preview    # serve the production build (with offline service worker) on :4174
```

Tests:

```bash
npm test                          # 32 engine tests: XP, levels, streaks, quests, SRS, planner, backup
npm run build && npm run e2e      # 18 end-to-end checks in Chromium on a Samsung Galaxy viewport
```

## Deployment

Every push to `main` runs the tests, builds the app and publishes it to GitHub Pages
(`.github/workflows/deploy.yml`). One-time setup: repository **Settings → Pages → Source:
GitHub Actions**.

`dist/` uses relative paths, so the same build also works on any other static HTTPS host
(Netlify, Cloudflare Pages…).

## Install on a Samsung Galaxy

1. Open the app's URL in **Chrome**.
2. Tap the **⋮** menu (top right).
3. Tap **Add to Home screen** (or **Install app**).
4. Confirm by tapping **Install**.
5. Open **LIFE XP** from the home screen. It runs full screen and works offline.

Tip: in Samsung Internet the option is ☰ → **Add page to** → **Home screen**, but Chrome is the
tested browser.

## Architecture: inputs → performance → outcomes → intelligence

| Layer | What | Where |
|---|---|---|
| Inputs | Habit ticks/counts, sleep, activity, water/meals/fruit & veg, revision sessions, topic reviews | `src/lib/actions.ts`, `src/lib/db.ts` |
| Performance | Day completion, consistency (rolling 7-day, weekly, monthly), streaks with shields | `src/lib/performance.ts` |
| Outcomes | XP ledger, levels, quests, achievements, titles, avatar unlocks | `src/lib/sync.ts`, `levels.ts`, `quests.ts`, `achievements.ts`, `avatar.ts` |
| Intelligence | Weekly coach, insights, spaced repetition, revision planner | `src/lib/coach.ts`, `insights.ts`, `academy/srs.ts`, `academy/planner.ts` |

The stack is React 19, Vite, Tailwind 4, Dexie (IndexedDB), Recharts, Radix UI and
vite-plugin-pwa (Workbox). It's the same proven stack as the existing app in this repository;
its UI primitives were copied in and re-themed, not imported.

### Key rules

- **XP can't be farmed.** Every award has a deterministic ID (`h:<habit>:<date>`, `dq:<date>:<quest>`,
  `rev:<session>`…). The reconciler can run any number of times and never awards the same thing twice.
  Unticking a mistaken tick removes that tick's XP. Missing a day never removes XP.
- **Levels:** XP to go from level L to L+1 = `100 × L^1.5` (100, 283, 520, 800, 1118…). Constants are
  in `src/lib/levels.ts`. Every level-up is celebrated exactly once.
- **Habit XP** is configurable per habit (1–40). The full-routine bonus (default 25) is set in Settings.
- **Streaks reward consistency, not perfection.** A day counts at 50%+ of its scheduled habits. Rest
  days never break a streak. Every 7 active days earns a shield (max 2) that covers one missed day.
- **Editing window:** today and yesterday only. A new day starts at 04:00 (configurable), so late
  ticks count for the right day. Dates are DST-safe.
- **Nothing is added automatically.** New habits, weekly quests and harder revision tiers are
  *offered*. The coach suggests one step at a time and never raises workload by itself.
- **Academy XP:** focused minutes (capped at the planned length, max 30), plus a method bonus
  (active recall/practice +10, exam-style +15…). Under 5 minutes earns 0. There's a 90 XP/day cap.
  Due reviews earn 15 XP each, once per topic per day. Topic status is always the student's
  choice; "Confident" needs confidence 4+ and "Retained" needs an Easy review after 14+ days.
- **Spaced repetition:** 1 → 3 → 7 → 14 → 30 → 60 days. Easy moves up the ladder, Medium repeats the
  gap, Difficult drops a step and comes back tomorrow.

See [ROADMAP.md](ROADMAP.md) for planned improvements.

### Privacy & safety

No accounts, real names, dates of birth, location, analytics, ads, leaderboards or public profiles.
No calorie, weight or appearance tracking. Health tracking is optional. All data stays on the
device. Use **Settings → Backup → Export** (saves via the Android share sheet, e.g. to Google Drive)
and **Restore** to move to a new phone.
