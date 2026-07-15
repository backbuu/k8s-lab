# Exercises 02 — RBAC: ServiceAccounts, Roles, Bindings

**Time limit:** 20:00   ·   **Cluster:** kind (cka-prep)   ·   **Weight:** 25%

Do these with a timer running. No solutions until you've attempted all.
**Verify every grant with `kubectl auth can-i --as=...` — an unverified answer is a wrong answer.**

Setup (run first, not scored):
```bash
kubectl create namespace dev
kubectl create namespace prod
```

## Task 1
In namespace `dev`, create a ServiceAccount named `deploy-bot`.

## Task 2
Grant `deploy-bot` read-only access (get, list, watch) to **pods in `dev` only**.
Prove it works, and prove it does *not* work in `prod`.

## Task 3
`deploy-bot` also needs to manage Deployments — create, update, delete — but still only in `dev`.
Add this without creating a second RoleBinding.

## Task 4
Create a ServiceAccount `node-bot` in `default` that can list **nodes** (a cluster-scoped
resource). Explain in one line why a Role would not work here.

## Task 5
There is a `pod-viewer` ClusterRole in the cluster (create it if absent: get/list/watch on pods).
Bind it so that SA `prod-viewer` (create it in `prod`) can read pods **in `prod` only** — reusing
that ClusterRole, without creating a new Role and without granting cluster-wide access.

## Task 6
Run `kubectl auth can-i --list -n dev --as=system:serviceaccount:dev:deploy-bot` and confirm the
final permission set is exactly what Tasks 2–3 asked for — no more. If `deploy-bot` can do
anything extra (e.g. read secrets, act in `prod`, touch nodes), you over-granted: find and fix it.

---
_Scoring:_ each task pass/fail. Milestone = % of tasks passed within the time limit. Record in `progress.md`.
