# Goal

## Ultimate goal
**Pass the CKA (Certified Kubernetes Administrator) exam.**

## Success criteria (definition of done)
- Score **≥ 66%** on a **full-length, timed** mock exam — the official CKA passing threshold.
- Achieve it **repeatably** (≥ 66% on 2 consecutive full mocks), not by luck once.
- Complete all mocks within the **2-hour** exam time limit.

## Constraints that mirror the real exam
- Performance-based: everything is done in a terminal against live clusters.
- Only exam-allowed documentation (kubernetes.io + subdomains) may be open.
- Multi-node kubeadm clusters; tasks span cluster ops, workloads, networking, storage, troubleshooting.
- Speed matters — allocate time by task weight, skip-and-return on hard tasks.

## Non-goals
- Deep application development. This is an *administrator* cert.
- Memorizing YAML by hand — use `kubectl create ... --dry-run=client -o yaml` and edit.
