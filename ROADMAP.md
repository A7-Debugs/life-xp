# LIFE XP roadmap

Notes for future updates. Review after the first trial runs.

## To revisit

### Habit XP editing could be gamed
Right now any habit's XP can be set from 1 to 40 (Habits → tap a habit), and the full-routine
bonus from 10 to 40 (Settings). A player can max everything out to level up faster, which
undermines the progression curve.

Options, in order of preference:
1. **Difficulty-based XP:** pick Easy / Medium / Hard and the app assigns 5 / 10 / 20. There's
   no raw number to inflate, and he keeps control over his habits.
2. **Fixed XP for library habits:** only custom habits get a narrow range (e.g. 5–15).
3. **Parent PIN** on XP and routine-bonus editing.

Warning sign during trials: several levels gained in the first week.
Relevant code: `src/routes/HabitEdit.tsx` (XP slider), `src/lib/habits.ts` (`XP_LIMITS`,
`clampXp`), `src/routes/Settings.tsx` (routine bonus), `src/lib/actions.ts` (`saveHabit`).
