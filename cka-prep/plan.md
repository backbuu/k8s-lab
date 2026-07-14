# Study Plan (full-time, ~3 weeks to exam-ready)

Time is weighted toward Troubleshooting (30%) and Cluster Architecture (25%).
Every day: **notes → timed drills → milestone test → log in `progress.md`**. A day is green only at test ≥ 80%.

## Week 1 — Foundations & control plane (Domains 1, 2, 4)
| Day | Topic | Deliverable | Milestone test |
| --- | ----- | ----------- | -------------- |
| 1 | kubeadm cluster bring-up; components | Working multi-node cluster | Init + join a node, verify `get nodes` Ready |
| 2 | RBAC: SA/Role/Binding, `can-i` | `notes/02` + exercises | Grant a SA least-privilege access, timed |
| 3 | etcd backup & restore | Snapshot + restored cluster | Restore etcd from snapshot < 15 min |
| 4 | Cluster upgrade (kubeadm) | Upgraded cluster one minor version | Drain, upgrade, uncordon, verify |
| 5 | Workloads: deployments, rollouts, probes | Rollout + rollback demo | Rolling update, rollback, add readiness probe |
| 6 | Scheduling: taints, affinity, resources | Pods placed as required | Schedule pod onto tainted node, timed |
| 7 | Storage: PV/PVC/StorageClass | Dynamic PVC + persistence proof | Provision + mount + survive pod restart |

**Week 1 review + mini-mock** → `reviews/week_01.md`

## Week 2 — Networking, troubleshooting, integration (Domains 3, 5)
| Day | Topic | Deliverable | Milestone test |
| --- | ----- | ----------- | -------------- |
| 8 | Services & endpoints | App exposed 3 ways | Fix a Service with no endpoints |
| 9 | Ingress | Host/path routing works | Route two paths to two services |
| 10 | NetworkPolicy | default-deny + allow rules | Isolate a namespace, allow one path |
| 11 | CoreDNS / cluster DNS | DNS troubleshooting notes | Fix broken DNS resolution |
| 12 | Troubleshooting: nodes & kubelet | Broken node restored | Recover a `NotReady` node, timed |
| 13 | Troubleshooting: workloads | Broken pods fixed | Diagnose CrashLoop/ImagePull/Pending set |
| 14 | Troubleshooting: control plane | Broken apiserver/etcd fixed | Restore a broken static-pod control plane |

**Week 2 review + half-length mock** → `reviews/week_02.md`

## Week 3 — Exam simulation & polish
| Day | Focus | Deliverable |
| --- | ----- | ----------- |
| 15 | Full timed mock #1 | Scorecard in `project/` |
| 16 | Remediate weak areas from mock #1 | Re-tests green |
| 17 | Full timed mock #2 (Killer.sh session 1) | Scorecard |
| 18 | Remediate + speed drills (kubectl/vim) | Faster times |
| 19 | Full timed mock #3 (Killer.sh session 2) | Scorecard |
| 20 | Remediate; doc-navigation speed | Notes in `sources.md` |
| 21 | Final full mock — must be ≥ 66% twice running | **Exam-ready** ✅ |

Adjust freely: if a milestone isn't green, repeat the day before advancing.
