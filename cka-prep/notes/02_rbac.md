# Note 02 — RBAC: ServiceAccounts, Roles, Bindings (Domain 1, weight 25%)

## Concept
RBAC answers one question: **can this subject perform this verb on this resource (in this namespace)?**
Four objects, split across two axes:

|  | Namespaced | Cluster-wide |
| --- | --- | --- |
| **Permissions** (what) | `Role` | `ClusterRole` |
| **Grant** (to whom) | `RoleBinding` | `ClusterRoleBinding` |

- **Role / ClusterRole** — a set of rules: `apiGroups` + `resources` + `verbs`. Purely additive;
  there is no "deny" rule in RBAC. No binding = no access (default deny).
- **RoleBinding / ClusterRoleBinding** — attaches a Role/ClusterRole to **subjects**
  (ServiceAccount, User, Group).

The combination that trips people up:
- `RoleBinding` → `Role`: permissions apply in the RoleBinding's namespace. Normal case.
- `RoleBinding` → **`ClusterRole`**: permissions apply **only in the RoleBinding's namespace**.
  This is the reusable pattern — define a ClusterRole once (e.g. "pod-reader"), bind it per-namespace.
- `ClusterRoleBinding` → `ClusterRole`: permissions apply **cluster-wide**, in every namespace.
- `ClusterRoleBinding` → `Role`: **not allowed**.

Cluster-scoped resources (nodes, PVs, namespaces themselves) can *only* be granted via a
ClusterRole + ClusterRoleBinding — a Role can never grant access to them.

**Subjects.** A ServiceAccount's full username is:
```
system:serviceaccount:<namespace>:<name>
```
Pods use SAs via `spec.serviceAccountName`; omitted = the namespace's `default` SA (which has
almost no permissions — that's intentional).

## Key commands
```bash
# create the pieces (imperative — much faster than YAML on the exam)
kubectl create serviceaccount deploy-bot -n dev
kubectl create role pod-reader --verb=get,list,watch --resource=pods -n dev
kubectl create rolebinding deploy-bot-reader \
  --role=pod-reader --serviceaccount=dev:deploy-bot -n dev

# cluster-scoped
kubectl create clusterrole node-reader --verb=get,list,watch --resource=nodes
kubectl create clusterrolebinding node-reader-binding \
  --clusterrole=node-reader --serviceaccount=default:node-bot

# reusable ClusterRole bound into ONE namespace only
kubectl create rolebinding dev-pod-reader \
  --clusterrole=pod-reader --serviceaccount=dev:deploy-bot -n dev

# THE verification command — impersonation
kubectl auth can-i list pods -n dev --as=system:serviceaccount:dev:deploy-bot     # -> yes/no
kubectl auth can-i delete pods -n kube-system --as=system:serviceaccount:dev:deploy-bot
kubectl auth can-i --list -n dev --as=system:serviceaccount:dev:deploy-bot        # everything it can do

# inspect
kubectl describe role pod-reader -n dev
kubectl get rolebinding -n dev -o wide      # shows role + subjects in one view
```

Handy: `--resource=pods/log`, `--resource=deployments.apps` for subresources / explicit apiGroups.

## Exam-legal doc path
kubernetes.io → Concepts → Security → **Using RBAC Authorization**. The page has copy-pasteable
Role/RoleBinding YAML near the top, and the "Referring to subjects" section for the
`system:serviceaccount:` format. Search the site for "RBAC".

## Gotchas / common mistakes
- **`apiGroups: ""`** (empty string) is the *core* group — pods, services, configmaps, secrets,
  nodes, PVs. Deployments/DaemonSets are `apps`; jobs are `batch`. Getting this wrong is the
  #1 cause of a rule that silently grants nothing.
- Resources are the **plural, lowercase API name** (`pods`, not `Pod`).
- Forgetting `-n <ns>` when creating the Role/RoleBinding — it lands in `default` and the grant
  appears to do nothing.
- `ClusterRoleBinding` when the task said "only in namespace X" — that's over-granting, and
  least-privilege tasks are scored on exactly this.
- A RoleBinding is **immutable in its `roleRef`** — you can't repoint it at a different Role;
  delete and recreate.
- Verbs are not CRUD: `get`, `list`, `watch`, `create`, `update`, `patch`, `delete`,
  `deletecollection`. "Read" = get+list+watch (a UI/controller that lists needs `list`, and
  `get` alone won't do it).

## Self-check questions
1. You bind a ClusterRole with a RoleBinding in namespace `dev`. Where do the permissions apply?
2. Why can a Role never grant access to nodes?
3. What's the full subject username for SA `ci` in namespace `build`?
4. A pod using SA `app` gets `forbidden` listing pods. What single command tells you if RBAC is
   the cause, without redeploying the pod?
5. Which apiGroup do Deployments live in, and what happens if you put them in `""`?
