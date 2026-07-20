# Progress

**Goal:** Pass CKA — score ≥ 66% on a full timed mock, repeatably. (See `goal.md`.)
**Start date:** 2026-07-14
**Log restarted:** 2026-07-16 — Days 00–02.01 archived to `reviews/progress_archive_days_00-02.md`
(nothing deleted; cleared backlog items and the full Day 02 → 02.01 RBAC arc live there).

## Status dashboard

| Domain | Weight | Confidence (0–5) | Last test | Status |
| ------ | :----: | :--------------: | :-------: | :----: |
| 1. Cluster Architecture | 25% | 4 | 100% (kubeadm/control plane, Day 01.02) | 🟩 |
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
  *Confirmed 2026-07-20: `multipass` is not installed. Nothing has moved on this since Day 01.*
- **🔴 Docker Hub pulls broken** (found Day 01.02) — `x509: certificate signed by unknown
  authority` on `docker.io`. Blocks Days 5, 8, 13. Use cached `registry.k8s.io` images meanwhile.
- **Mechanism vs. conclusion** (Day 01.02) — reached the right answer on DaemonSet drains from the
  wrong mechanism. Right conclusions built on hand-wavy mechanisms survive easy questions and fail
  hard ones. When an answer feels obvious, state *why* it's true, not just *that* it is.
- **Mirror pods / static-pod removal** (Day 01.02) — behaviour known, concept unnamed, and the
  manifest file-move not volunteered as the removal method. Re-test before Day 14.
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

### Day 01.02 — 2026-07-20 — kubeadm bring-up & control plane (Domain 1) — **re-run**
- **Deliverable:** `exercises/01_kubeadm_bringup/solutions.md` — written from this run, covering
  all six `tasks_0102.md` tasks (the drill file existed from a previous session but had never been
  attempted or logged; the topic folder had no solutions file at all until today).
- **Studied:** No new notes — re-runs go straight to the drill. `notes/01_kubeadm_bringup.md` stands.
- **Drilled:** `exercises/01_kubeadm_bringup/tasks_0102.md` — 6 tasks, fresh variant (runtime
  inspection, etcd manifest, kubelet unit/config, apiserver cert break + recovery, cordon/drain,
  static-pod theory). No reuse of the Day 01 scheduler-kill or join-command drills.
- **Milestone test:** 100% (6/6) → 🟩
- **Time:** **unmeasured, not failed.** Well past the 20:00 limit, but most of the overrun was
  diagnosing two pre-existing cluster faults (below) that are not part of the drill. Logged
  score-only per the two-bars rule; does *not* clear the open speed item, which is RBAC anyway.
- **Missed / weak:** Nothing failed. Two precision gaps, both corrected in-session:
  - **DaemonSet drain mechanism** — answered "the controller reschedules them regardless", which
    is the right conclusion from the wrong mechanism. The actual reason: cordon applies the
    `node.kubernetes.io/unschedulable` taint and DaemonSet pods *tolerate* it, so eviction is
    meaningless rather than merely futile. Conclusion-first reasoning that skips the mechanism is
    the thing to watch — it holds up on easy questions and breaks on hard ones.
  - **Mirror pods** — knew the behaviour ("kubelet recreates it") but not the concept or the name,
    and did not volunteer the file-move as the actual removal method. Day 14 depends on that move.
- **Since last attempt:** Day 01 and 01.01 were both 100% on the *same* task set; this was a
  genuinely different one — including a destructive apiserver break and recovery that neither
  earlier run covered — and still came back 6/6. Domain 1 fundamentals are repeatable across
  scenarios, not just across repetitions. Strongest moments: `systemctl cat` chosen unprompted
  over `status`; the peer-vs-client cert split taken correctly first try; `crictl ps -a` reached
  for without a hint once the apiserver was down.
- **Tomorrow:** Day 3 — etcd backup & restore. **Still blocked**: `multipass` is not installed
  (verified today). Either install it, or take Day 5 (workloads/rollouts) out of sequence — but
  see the registry fault below, which blocks Day 5 too.
- **Lab state:** NOT cleaned. `drain-victim` deleted by the drain (expected). `worker2` uncordoned.
  Apiserver manifest restored from `/root/kube-apiserver.yaml.bk` — **that backup is still on the
  control-plane node**. Cluster healthy, 3 nodes Ready, nothing unhealthy cluster-wide.

#### Environment faults found today (not knowledge gaps — both pre-existing)
- **🔴 Docker Hub image pulls fail cluster-wide.** `docker.io/library/nginx` →
  `x509: certificate signed by unknown authority`. TLS interception (proxy/VPN/Rancher Desktop)
  presenting a cert the node's trust store rejects. **Blocks Day 5, 8, 13** — every drill that
  assumes `nginx`/`busybox` are pullable. Workaround used today: `registry.k8s.io/pause:3.10`
  with `--image-pull-policy=IfNotPresent` (already cached on all nodes). `registry.k8s.io` was
  not tested and may be unaffected. **Fix before Day 5.**
- **🟡 `local-path-provisioner` was in CrashLoopBackOff** (`local-path-storage` ns), spotted in
  the kubelet journal. Recovered on its own during the session. It is kind's dynamic provisioner —
  if it relapses, Day 7 PVCs will hang `Pending` forever and it will look like a storage-knowledge
  failure. Check it *before* starting Day 7.

---

_Previous entries: Days 00–02.01 in `reviews/progress_archive_days_00-02.md`._
