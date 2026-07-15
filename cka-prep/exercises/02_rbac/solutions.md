# Solutions 02 — RBAC: ServiceAccounts, Roles, Bindings

> Only open after attempting `tasks.md`. Note *why*, not just the command.

## Task 1
```bash
k create serviceaccount deploy-bot -n dev
```
**Why:** Nothing else needed — a SA with no binding has no permissions. Default deny is the baseline.

## Task 2
```bash
k create role pod-reader --verb=get,list,watch --resource=pods -n dev
k create rolebinding pod-reader-binding --role=pod-reader --serviceaccount=dev:deploy-bot -n dev

k auth can-i list pods -n dev  --as=system:serviceaccount:dev:deploy-bot   # yes
k auth can-i list pods -n prod --as=system:serviceaccount:dev:deploy-bot   # no
```
**Why:** A RoleBinding grants only inside its own namespace, so `prod` is denied for free — no
explicit deny needed (RBAC has none). The negative check is the half that proves scoping.

## Task 3
```bash
k edit role pod-reader -n dev
```
```yaml
rules:
- apiGroups: [""]          # core group
  resources: ["pods"]
  verbs: ["get","list","watch"]
- apiGroups: ["apps"]      # deployments live in apps, NOT ""
  resources: ["deployments"]
  verbs: ["create","update","delete"]
```
**Why:** "Without a second RoleBinding" is the hint that you extend the **Role** — the binding
already points at it, so new rules take effect immediately. The rule is a *second list entry*,
not extra verbs on the pods rule: bolting `create,update,delete` onto the pods rule grants
nothing for deployments **and** silently breaks the read-only guarantee from Task 2.

Verify all four corners, not just one:
```bash
k auth can-i delete pods        -n dev --as=system:serviceaccount:dev:deploy-bot  # no
k auth can-i update deployments -n dev --as=system:serviceaccount:dev:deploy-bot  # yes
```

## Task 4
```bash
k create serviceaccount node-bot
k create clusterrole node-reader --verb=get,list,watch --resource=nodes
k create clusterrolebinding node-bot-binding \
  --clusterrole=node-reader --serviceaccount=default:node-bot

k auth can-i list nodes --as=system:serviceaccount:default:node-bot   # yes
```
**Why:** A Role is namespace-scoped; Nodes are cluster-scoped, so no Role can ever name them.
Cluster-scoped resource ⇒ ClusterRole **+ ClusterRoleBinding**. Note the ClusterRole alone grants
nothing — a role without a binding is attached to no one.

## Task 5
```bash
k create serviceaccount prod-viewer -n prod
k create clusterrole pod-viewer --verb=get,list,watch --resource=pods
k create rolebinding pod-viewer-binding \
  --clusterrole=pod-viewer --serviceaccount=prod:prod-viewer -n prod
```
**Why:** The reusable pattern — **RoleBinding → ClusterRole** applies the ClusterRole's rules
*only in the RoleBinding's namespace*. Define the permission set once cluster-wide, then hand it
out per-namespace. A ClusterRoleBinding here would have granted pod-read in **every** namespace:
the classic over-grant. `--clusterrole=` with `-n prod` is the whole trick.

Subject format is exact: `system:serviceaccount:<ns>:<name>` — `prod:prod-viewer`, not
`default:pod-viewer`. `roleRef` is immutable; `subjects` can be edited.

## Task 6
```bash
k auth can-i --list -n dev --as=system:serviceaccount:dev:deploy-bot
```
Expected real grants:
```
pods              [get list watch]
deployments.apps  [create update delete]
```
**Why:** The extra `selfsubject*reviews` and non-resource URLs (`/healthz`, `/version`, `/api/*`)
are **not** over-grants — they come from the built-in `system:basic-user` and
`system:public-info-viewer` ClusterRoleBindings attached to the `system:authenticated` group, so
every authenticated subject has them. They enable API discovery and self-access checks. Leave
them alone; stripping them isn't possible via your Role anyway.
