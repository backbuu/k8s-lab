# Exercises 02.01 — RBAC re-test (fresh scenario)

**Time limit:** 15:00 (strict)   ·   **Cluster:** kind (cka-prep)   ·   **Weight:** 25%

New objects, same muscles. This drill targets the four Day 02 backlog items:
apiGroups literals · RoleBinding→ClusterRole · extend-vs-widen · subject precision.

**Verify every grant with `kubectl auth can-i --as=...` — an unverified answer is a wrong answer.**

Setup (already applied, not scored): namespaces `build` and `staging` exist.

## Task 1
In namespace `build`, create a ServiceAccount named `ci-runner`.
Before binding anything, prove it currently cannot list pods in `build`.

## Task 2
Grant `ci-runner` permission to **create, get, and delete Jobs in `build` only**.
Prove it works in `build` and fails in `staging`.

> Jobs are not in the core group. Confirm the group before you write the rule.

## Task 3
`ci-runner` now also needs **read-only (get, list, watch) access to ConfigMaps** in `build`
— without creating a second Role and without creating a second RoleBinding.
Afterwards, prove its Job permissions are still intact and that it still **cannot** delete
ConfigMaps.

## Task 4
Create a ClusterRole `pv-inspector` that can `get`/`list` **PersistentVolumes**.
Create SA `storage-bot` in `staging` and give it exactly that cluster-wide.
In one line: why can this not be done with a Role + RoleBinding?

## Task 5
A ClusterRole named `secret-reader` should exist (get/list on secrets — create it if absent).
Bind it so SA `ci-runner` can read Secrets **in `staging` only** — reusing that ClusterRole,
creating no new Role, and granting nothing cluster-wide.

Then prove the negative that matters: `ci-runner` must **not** be able to read secrets in `build`.

## Task 6
Run `kubectl auth can-i --list -n build --as=system:serviceaccount:build:ci-runner`.
State which entries are your grants and which are the authenticated-user baseline.
Then answer: is there any over-grant? If yes, fix it.

---
_Scoring:_ each task pass/fail. Milestone = % of tasks passed within 15:00. Record in `progress.md`.
