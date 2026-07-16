# Exercises 01.02 — kubeadm bring-up & control plane (fresh variant)

**Time limit:** 20:00   ·   **Cluster:** kind (cka-prep)   ·   **Weight:** 25% (Domain 1)

Timer runs from Task 1. No solutions until all six are attempted. Verify every answer against the
cluster — an answer you didn't check doesn't count.

Different scenario from Day 01 / 01.01: no reuse of the scheduler-kill or the join-command drill.

## Task 1
Without using `kubectl`, determine which container runtime the control-plane node is using and
which version. Then, still without `kubectl`, list the running control-plane containers directly
from the runtime.

## Task 2
`etcd` is a static pod here. From its manifest alone, report:
- the data directory it persists to,
- the client listen URL and port,
- the three cert/key files it uses for **peer** traffic (not client traffic).

## Task 3
The kubelet is not a static pod. Find, on the control-plane node:
- the systemd unit that runs it and its current state,
- the config file that supplies its runtime settings (not the flags),
- the `staticPodPath` value from that config — and confirm it matches where the manifests live.

## Task 4
Break the apiserver's *client* certificate trust: on the control-plane node, edit the apiserver
static pod manifest so `--client-ca-file` points at a path that does not exist. Observe what
happens to the apiserver, prove it from the node (not `kubectl` — it will be down), then restore
it and confirm the API comes back.

## Task 5
A pod scheduled to `cka-prep-worker2` must be evicted and the node kept empty. Cordon and drain
it, prove nothing schedulable remains, then return it to service. State which flag you needed for
the DaemonSet pods and why they don't leave.

## Task 6
Explain, with evidence from this cluster, why `etcd`, `kube-apiserver`, `kube-controller-manager`
and `kube-scheduler` come up **before** any scheduler exists to place them — and what would happen
to a static pod if you tried to `kubectl delete` it.

---
_Scoring:_ each task pass/fail. Milestone = % passed within the time limit. Record in `progress.md`.
