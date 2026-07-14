# Progress

**Goal:** Pass CKA — score ≥ 66% on a full timed mock, repeatably. (See `goal.md`.)
**Start date:** 2026-07-14

## Status dashboard

| Domain | Weight | Confidence (0–5) | Last test | Status |
| ------ | :----: | :--------------: | :-------: | :----: |
| 1. Cluster Architecture | 25% | 2 | 100% | 🟨 |
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
- _(populate from milestone-test misses)_

---

## Daily log

> Copy the template below for each new day. Newest at the top.

### Day NN — YYYY-MM-DD — <topic> (Domain <n>)
- **Deliverable:** <the artifact committed today — note / exercise set / project increment>
- **Studied:** <what was covered> → `notes/NN_topic.md`
- **Drilled:** `exercises/NN_topic/` — <count> tasks, timed <mm:ss>
- **Milestone test:** <score>%  → 🟩 / 🟨 (green needs ≥ 80%)
- **Missed / weak:** <list; add to backlog above>
- **Tomorrow:** <next topic>

---

### Day 01 — 2026-07-14 — kubeadm cluster bring-up + control-plane components (Domain 1)
- **Deliverable:** 3-node kind cluster (`cka-prep`) up and Ready; static pod manifests inspected.
- **Studied:** kubeadm init/join flow, static pods vs DaemonSets, control-plane vs worker
  components → `notes/01_kubeadm_bringup.md`
- **Drilled:** `exercises/01_kubeadm_bringup/` — 5 tasks, timed
- **Milestone test:** 100% → 🟩 (5/5: static pod manifests, apiserver flag inspection,
  scheduler-down/up failure sim, kubeadm join generation, component placement explanation)
- **Missed / weak:** kind can't drill real `kubeadm init`/`join` mechanics — need Linux VMs
  before Day 3 (etcd backup/restore) and Day 4 (cluster upgrade).
- **Tomorrow:** Day 2 — RBAC: SA/Role/Binding, `kubectl auth can-i`.

---

### Day 00 — 2026-07-14 — Setup
- **Deliverable:** `cka-prep/` scaffold created (CLAUDE.md, goal, map, sources, progress, plan).
- **Studied:** Reviewed the CKA blueprint & domain weights in `01_map.md`.
- **Drilled:** —
- **Milestone test:** N/A (setup day)
- **Missed / weak:** —
- **Tomorrow:** Day 1 — Domain 1: kubeadm cluster bring-up + RBAC basics.
