# Progress — ARCHIVE (Days 00 – 02.01)

> **Archived 2026-07-16.** The append-only daily log for Days 00 through 02.01, moved out of
> `progress.md` at the learner's request to clear the active dashboard. **Nothing here was deleted
> or edited** — every score stands exactly as recorded. The live file (`../progress.md`) carries
> forward only the still-open backlog; cleared/green items are documented here.
>
> Worth re-reading before a mock: the RBAC arc (Day 02 → Day 02.01) documents a failure mode that
> repeated *exactly* across two attempts before going green.

**Goal:** Pass CKA — score ≥ 66% on a full timed mock, repeatably. (See `goal.md`.)
**Start date:** 2026-07-14

## Status dashboard

| Domain | Weight | Confidence (0–5) | Last test | Status |
| ------ | :----: | :--------------: | :-------: | :----: |
| 1. Cluster Architecture | 25% | 3 | 83% (RBAC re-test, Day 02.01) | 🟩 |
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
- ~~**RBAC `apiGroups` values**~~ — green (Day 02.01 / 3b: `batch`, `apps`, `""` all correct unaided).
- ~~**RoleBinding → ClusterRole pattern**~~ — green (Day 02.01 T5: correct first try, incl.
  cross-namespace subject and the `kube-system` no-leak proof).
- ~~**Extend vs. widen**~~ — green (Day 02.01 / 3b: four rule entries, no prior grant disturbed).
  *Note: failed once more on first contact (T3) before going green — watch for regression.*
- ~~**Subject precision**~~ — green (Day 02.01 T4: typo caught from a minimal nudge).
- **RBAC speed** — 🟨 knowledge is green, but the 15:00 limit has never been met. Needs one clean
  timed run.
- **Over-grant vs. granted-as-specified** — over-grant means *exceeds the ask*; check each rule
  against the task text before calling it a violation (Day 02.01 T6).
- **Trust the question, not just the answer** — a malformed `--as=` returns a plausible `no`.
  When a check surprises you, re-read the command before debugging the config.
- kubeadm `init`/`join` mechanics — kind can't drill these; need Linux VMs before Day 3/Day 4.

---

## Daily log

> Copy the template below for each new day. Newest at the top.
> **Re-runs are appended as `Day NN.01`, `Day NN.02` — never overwrite a past entry.** (See `CLAUDE.md`.)

### Day NN[.RR] — YYYY-MM-DD — <topic> (Domain <n>)
- **Deliverable:** <the artifact committed today — note / exercise set / project increment>
- **Studied:** <what was covered> → `notes/NN_topic.md`
- **Drilled:** `exercises/NN_topic/` — <count> tasks, timed <mm:ss>
- **Milestone test:** <score>%  → 🟩 / 🟨 (green needs ≥ 80%)
- **Missed / weak:** <list; add to backlog above>
- **Since last attempt:** <re-runs only: what changed, which weak areas are now green>
- **Tomorrow:** <next topic>

---

### Day 02.01 — 2026-07-15 — RBAC re-test (Domain 1) — **re-run**
- **Deliverable:** `exercises/02_rbac/tasks_0201.md` — fresh scenario (build/staging, jobs/
  configmaps/deployments/secrets/PVs), no reuse of the Day 02 objects.
- **Drilled:** 6 tasks + 1 remediation variant (Task 3b), on the kind cluster.
- **Milestone test:** 83% (5/6) → 🟩 — **but not inside the 15:00 limit** (~30 min elapsed,
  including my inserted 3b drill and a broken Task 5 I had to reissue). A clean *timed* run
  is still unproven; the knowledge bar is green, the speed bar is not.
- **Missed / weak:**
  - **Task 3 — failed.** Repeated the Day 02 failure mode exactly: added `list,watch` to the
    `jobs` rule instead of adding a `configmaps` rule; target resource absent entirely.
    Needed a conceptual hint (a rule is apiGroups × resources × verbs; new resource = new
    list entry) before correcting. Self-corrected the widened Jobs verbs unprompted.
  - **Task 4 — subject typo** (`systemaccount:` vs `system:serviceaccount:`) returned a
    plausible `no` that tested nothing. Caught it from a minimal hint. Lesson logged: when a
    check surprises you, verify the *question* before debugging the *answer*.
  - **Task 6 —** called `secrets list` an over-grant; it was granted-as-specified by 3b.
    Reviewer instinct right, spec-check wrong. Over-grant = exceeds the ask.
  - Strong: `jobs.batch` used unprompted (apiGroup pinned at the source); RoleBinding→ClusterRole
    correct first try incl. cross-namespace subject; `staging` yes / `kube-system` no leak proof;
    grants-vs-baseline split correct.
- **Since last attempt:** Task 3b (fresh variant, no hints) came back clean on all ten corners —
  four rule entries, `apps` + `""` correct, no prior grant disturbed. On that evidence:
  **apiGroups literals → green**, **extend-vs-widen → green**, **RoleBinding→ClusterRole → green**,
  **subject precision → green** (typo caught unaided from a nudge). Day 02's four backlog items
  are cleared. The gap closed *during* the session rather than before it — hence Task 3 stands
  as a fail.
- **Tomorrow:** Day 3 — etcd backup & restore. **Blocked on kind**: needs real kubeadm Linux VMs
  (multipass). Optional: one clean 15:00 RBAC run to close the speed gap.
- **Lab state:** NOT cleaned — `build`/`staging`, ClusterRoles `pv-inspector`/`secret-reader`,
  CRB `pv-inspector-binding`, SAs `ci-runner`/`storage-bot` still live.

---

### Day 02 — 2026-07-15 — RBAC: SA / Role / Binding / `can-i` (Domain 1)
- **Deliverable:** `notes/02_rbac.md` + `exercises/02_rbac/` (tasks + solutions); least-privilege
  grants built and verified on the kind cluster.
- **Studied:** Role vs ClusterRole vs the two binding types, `system:serviceaccount:<ns>:<name>`,
  impersonation with `--as=`, apiGroups → `notes/02_rbac.md`
- **Drilled:** `exercises/02_rbac/` — 6 tasks
- **Milestone test:** 67% (4/6) → 🟨 (green needs ≥ 80%) — **re-test required**
- **Missed / weak:**
  - **Task 3** — widened the `pods` rule instead of adding a `deployments` rule; invented an
    apiGroup (`deployment role` instead of `apps`); shipped an incomplete verb list. Three
    corrections needed. This is the dangerous failure mode: it *looks* applied but grants nothing.
  - **Task 5** — needed the RoleBinding→ClusterRole pattern handed over, then bound the wrong
    subject (`default:pod-viewer` vs `prod:prod-viewer`).
  - Strong: cluster-scoped reasoning (Task 4) and reading `can-i --list` output correctly (Task 6).
- **Tomorrow:** **Day 02.01** — RBAC re-test (fresh drill, strict 15 min; must clear the four
  backlog items above) → then Day 3 — etcd backup & restore. Day 3/4 need real kubeadm VMs;
  kind cannot do them.
- **Lab state:** cleaned 2026-07-15 (ns `dev`/`prod`, ClusterRoles `node-reader`/`pod-viewer`,
  CRB `node-bot-binding`, SA `node-bot` all deleted); cluster healthy, 3 nodes Ready.

---

### Day 01.01 — 2026-07-14 — kubeadm cluster bring-up (Domain 1) — **re-run**
- **Deliverable:** kind cluster torn down and rebuilt from scratch; `notes/01_kubeadm_bringup.md`
  + `exercises/01_kubeadm_bringup/` regenerated.
- **Studied:** Same material as Day 01 (learner-requested clean re-run, not a remediation).
- **Drilled:** `exercises/01_kubeadm_bringup/` — 5 tasks
- **Milestone test:** 100% → 🟩 (5/5, same task set)
- **Since last attempt:** Identical 5/5 result on a fresh cluster — bring-up + component knowledge
  is repeatable, not first-time luck. No new weak areas. The Linux-VM gap below is unchanged
  (it's an environment limit, not a knowledge gap, so a re-run can't clear it).
- **Tomorrow:** Day 2 — RBAC: SA/Role/Binding, `kubectl auth can-i`.

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
