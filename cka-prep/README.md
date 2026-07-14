# cka-prep — CKA Exam Learning Project

A self-coached, hands-on study workspace to **pass the Certified Kubernetes Administrator (CKA) exam**.
Claude Code acts as the study coach — rules live in `CLAUDE.md`.

## Layout
| File / dir | Purpose |
| ---------- | ------- |
| `CLAUDE.md` | Coaching rules (deliverable/day, daily milestone test, exam simulation). |
| `goal.md` | Fixed target: ≥ 66% on a full timed mock, repeatably. |
| `01_map.md` | CKA domain knowledge map + weights + skill checklist. |
| `plan.md` | ~3-week full-time day-by-day plan. |
| `progress.md` | **Single source of truth** — daily log, status dashboard, mock history. |
| `sources.md` | Approved study material (exam-legal docs first). |
| `sources/` | Reusable configs (e.g. `kind-multinode.yaml`). |
| `notes/` | One note per topic (`NN_topic.md`). |
| `exercises/` | Timed drills per topic: `tasks.md` + `solutions.md`. |
| `reviews/` | Weekly retros + mock scorecards. |
| `project/` | Capstone: full timed mock exams + exam-ready checklist. |

## The daily loop
1. Open `progress.md` → pick today's topic from `01_map.md`.
2. Study → `notes/`.  3. Drill (timed) → `exercises/`.  4. Milestone test.
5. Log the day in `progress.md` (green only if test ≥ 80%).

**Start:** ask Claude — *"Start Day 1"* — and it will run the loop against `plan.md`.
