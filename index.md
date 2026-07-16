# CKA Prep

A self-coached, hands-on study workspace for the **Certified Kubernetes Administrator** exam.
Every day ships a deliverable and ends with a timed milestone test — no passive reading days.

**Target:** ≥ 66% on a full 2-hour mock, on two consecutive mocks.
**Started:** 2026-07-14.

## Where things stand

| Domain | Weight | Status |
| ------ | :----: | :----: |
| 1. Cluster Architecture | 25% | 🟩 83% (RBAC re-test) |
| 2. Workloads & Scheduling | 15% | ⬜ not started |
| 3. Services & Networking | 20% | ⬜ not started |
| 4. Storage | 10% | ⬜ not started |
| 5. Troubleshooting | 30% | ⬜ not started |

The live dashboard, daily log, and weak-area backlog are in **[progress.md](cka-prep/progress.md)** —
the single source of truth for this project.

## The two bars

Two numbers doing two different jobs:

- **A day is green at milestone score ≥ 80%.** Score-only — time is tracked but not gating. Missing
  a time limit logs a *speed* item to the backlog instead, cleared by its own clean timed run.
- **Exam-ready is ≥ 66% on a full 2-hour mock, twice running.**

## Start here

| Page | What it is |
| ---- | ---------- |
| [Goal](cka-prep/goal.md) | The fixed target and success criteria. |
| [Domain map](cka-prep/01_map.md) | CKA domains, weights, and the skill checklist. |
| [Plan](cka-prep/plan.md) | The ~3-week full-time, day-by-day plan. |
| [Progress](cka-prep/progress.md) | Daily log, dashboard, mock history, weak areas. |
| [Sources](cka-prep/sources.md) | Approved material — exam-legal docs first. |

## Notes

- [Kubeadm bring-up](cka-prep/notes/01_kubeadm_bringup.md) — control-plane components, `init`/`join`.
- [RBAC](cka-prep/notes/02_rbac.md) — ServiceAccounts, Roles, Bindings, `auth can-i`.

## Drills

Timed task sets, no answers until attempted. A re-run gets a fresh scenario targeting the previous
attempt's weak areas, and its own file (`tasks_0201.md` = Day 02.01).

- [Kubeadm bring-up](cka-prep/exercises/01_kubeadm_bringup/tasks.md) · [re-run 01.02](cka-prep/exercises/01_kubeadm_bringup/tasks_0102.md)
- [RBAC](cka-prep/exercises/02_rbac/tasks.md) · [re-run 02.01](cka-prep/exercises/02_rbac/tasks_0201.md)

## Reviews & capstone

- [Archive: Days 00–02.01](cka-prep/reviews/progress_archive_days_00-02.md) — full history. Nothing
  is ever deleted here; the RBAC arc across Day 02 → 02.01 is the part worth re-reading.
- [Capstone](cka-prep/project/README.md) — full timed mocks and the exam-ready checklist.

---

Lab manifests (mealie, monitoring, deployments) live in the same repo but are not part of this site.
See [the repository](https://github.com/backbuu/k8s-lab).
