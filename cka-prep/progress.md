# Progress

**Goal:** Pass CKA — score ≥ 66% on a full timed mock, repeatably. (See `goal.md`.)
**Start date:** 2026-07-14
**Log cleared:** 2026-07-22 — daily log wiped at the learner's explicit request, overriding the
append-only rule in `CLAUDE.md`. Days 00–02.01 remain in `reviews/progress_archive_days_00-02.md`;
Day 01.02 was *not* archived and survives only in git (`git show e178c12:cka-prep/progress.md`).

## Status dashboard

| Domain | Weight | Confidence (0–5) | Last test | Status |
| ------ | :----: | :--------------: | :-------: | :----: |
| 1. Cluster Architecture | 25% | 4 | 100% (kubeadm/control plane, Day 01.02 — see git) | 🟩 |
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
- ~~**🔴 Docker Hub pulls broken** — `x509: certificate signed by unknown authority` on
  `docker.io`.~~ **Cleared 2026-07-22** by the moby → containerd engine switch:
  `nerdctl --namespace k8s.io pull docker.io/library/busybox` now succeeds. Days 5, 8, 13 unblocked.
- ~~**🔴 kind cluster is dead**~~ — **resolved same day, 2026-07-22.** Losing the Docker socket did
  break `kind`, but the fix was one env var, not a migration:
  `export KIND_EXPERIMENTAL_PROVIDER=nerdctl`. Cluster rebuilt from `sources/kind-multinode.yaml`
  — 3 nodes Ready, v1.36.1, full static-pod control plane. **Not persisted**: set it in every shell
  that runs `kind`.
- **🟡 Two containerd namespaces** (2026-07-22) — kind's *node containers* are in `default`
  (`nerdctl exec cka-prep-control-plane bash`, no flag); *cluster images* are in `k8s.io`
  (`nerdctl --namespace k8s.io images`). Guessing wrong returns `no such container` or an empty
  list, which reads as a broken cluster. Cost a wrong turn today.
- **Mechanism vs. conclusion** (Day 01.02) — reached the right answer on DaemonSet drains from the
  wrong mechanism. Right conclusions built on hand-wavy mechanisms survive easy questions and fail
  hard ones. When an answer feels obvious, state *why* it's true, not just *that* it is.
- **Mirror pods / static-pod removal** (Day 01.02) — behaviour known, concept unnamed, and the
  manifest file-move not volunteered as the removal method. Re-test before Day 14.
- **Mac-vs-node context** — node-level tools (`crictl`, `systemctl status kubelet`,
  `/etc/kubernetes/manifests`) only exist *inside* the node. `kubectl` works from anywhere with a
  kubeconfig. Knowing which side of that line you're on underpins most of Domain 5 (30%).
  Entry depends on the runtime: `nerdctl --namespace k8s.io exec -it <node> bash` or
  `rdctl shell` on containerd, `ssh` on the real exam. (`docker exec` no longer exists here.)

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

_Day 01.02 (2026-07-20, kubeadm bring-up re-run, 100%) was cleared from this file on 2026-07-22 at
the learner's request. Recover with `git show e178c12:cka-prep/progress.md`._
_Earlier entries: Days 00–02.01 in `reviews/progress_archive_days_00-02.md`._
