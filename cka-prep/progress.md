# Progress

**Goal:** Pass CKA — score ≥ 66% on a full timed mock, repeatably. (See `goal.md`.)
**Start date:** 2026-07-14
**Log restarted:** 2026-07-16 — Days 00–02.01 archived to `reviews/progress_archive_days_00-02.md`
(nothing deleted; cleared backlog items and the full Day 02 → 02.01 RBAC arc live there).

## Status dashboard

| Domain | Weight | Confidence (0–5) | Last test | Status |
| ------ | :----: | :--------------: | :-------: | :----: |
| 1. Cluster Architecture | 25% | 3 | 83% (RBAC re-test, Day 02.01 — archived) | 🟩 |
| 2. Workloads & Scheduling | 15% | 0 | — | ⬜ |
| 3. Services & Networking | 20% | 0 | — | ⬜ |
| 4. Storage | 10% | 0 | — | ⬜ |
| 5. Troubleshooting | 30% | 0 | — | ⬜ |

Legend: ⬜ not started · 🟨 in progress · 🟩 green (milestone ≥ 80%)

## Mock exam history

| Date | Mock | Score | Time used | Pass (≥66%)? | Weak areas |
| ---- | ---- | :---: | :-------: | :----------: | ---------- |
| — | — | — | — | — | — |

## Weak-area backlog (re-test until green)

Carried forward from the archive — these are the items still **open**. Green/cleared items are not
repeated here; see `reviews/progress_archive_days_00-02.md` for the full record.

- **RBAC speed** — 🟨 knowledge is green (83%, Day 02.01), but the 15:00 limit has never been met.
  Needs one clean timed run. *This is the oldest open item.*
- **Over-grant vs. granted-as-specified** — over-grant means *exceeds the ask*; check each rule
  against the task text before calling it a violation.
- **Trust the question, not just the answer** — a malformed `--as=` returns a plausible `no`.
  When a check surprises you, re-read the command before debugging the config.
- **kubeadm `init`/`join` mechanics** — kind can't drill these; needs real Linux VMs (multipass)
  before Day 3 (etcd restore) and Day 4 (upgrade). Environment gap, not a knowledge gap.
- **Mac-vs-node context** — node-level tools (`crictl`, `systemctl status kubelet`,
  `/etc/kubernetes/manifests`) only exist *inside* the node (`docker exec -it <node> bash`; `ssh`
  on the real exam). `kubectl` works from anywhere with a kubeconfig. Knowing which side of that
  line you're on underpins most of Domain 5 (30%).

---

## Daily log

> Copy the template below for each new day. Newest at the top.
> **Re-runs are appended as `Day NN.01`, `Day NN.02` — never overwrite a past entry.** (See `CLAUDE.md`.)
> Days 00–02.01 are in `reviews/progress_archive_days_00-02.md`.

### Day NN[.RR] — YYYY-MM-DD — <topic> (Domain <n>)
- **Deliverable:** <the artifact committed today — note / exercise set / project increment>
- **Studied:** <what was covered> → `notes/NN_topic.md`
- **Drilled:** `exercises/NN_topic/` — <count> tasks, timed <mm:ss>
- **Milestone test:** <score>%  → 🟩 / 🟨 (green needs ≥ 80%)
- **Missed / weak:** <list; add to backlog above>
- **Since last attempt:** <re-runs only: what changed, which weak areas are now green>
- **Tomorrow:** <next topic>

---

_No days logged since the 2026-07-16 restart. Next entry goes here._
