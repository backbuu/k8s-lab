# Exercises 01 — kubeadm cluster bring-up + control-plane components

**Time limit:** 20:00   ·   **Cluster:** kind (cka-prep)   ·   **Weight:** 25%

Do these with a timer running. No solutions until you've attempted all. Verify each answer.

## Task 1
List every static pod manifest on the control-plane node and identify which component each
one runs, without using `kubectl` (inspect the node's filesystem directly).

## Task 2
Find the exact `--service-cluster-ip-range` (or equivalent apiserver flag) currently configured
on the running apiserver, by reading its static pod manifest — not `kubectl describe`.

## Task 3
Simulate a control-plane failure: stop the kube-scheduler static pod by moving its manifest out
of `/etc/kubernetes/manifests/` on the control-plane node. Confirm new Pods stay `Pending`.
Then restore it and confirm scheduling resumes.

## Task 4
Generate a fresh `kubeadm join` command (token + discovery hash) for this cluster — even though
you can't actually join a new node to kind, produce the command as if you were about to.

## Task 5
Identify, from `kubectl get pods -n kube-system -o wide`, which node each control-plane
component and each DaemonSet (kube-proxy, CNI) pod is running on, and explain why the
distribution looks the way it does.

---
_Scoring:_ each task pass/fail. Milestone = % of tasks passed within the time limit. Record in `progress.md`.
