# 01 — CKA Domain Knowledge Map

The CKA exam is organized into 5 weighted domains. Time and drill volume should be **proportional to weight**.
Check off each sub-skill only when a milestone test on it scores ≥ 80%.

| # | Domain | Weight | Priority |
| - | ------ | :----: | -------- |
| 1 | Cluster Architecture, Installation & Configuration | **25%** | High |
| 2 | Workloads & Scheduling | 15% | Medium |
| 3 | Services & Networking | **20%** | High |
| 4 | Storage | 10% | Low |
| 5 | Troubleshooting | **30%** | Highest |

Suggested sequence: **1 → 2 → 4 → 3 → 5** (build foundations, then finish on the heaviest, most integrative domain).

---

## 1. Cluster Architecture, Installation & Configuration — 25%
- [ ] RBAC: Roles, ClusterRoles, RoleBindings, ServiceAccounts, `kubectl auth can-i`
- [ ] Provision a cluster with **kubeadm** (init, join, control-plane vs worker)
- [ ] **Cluster upgrades** with kubeadm (`kubeadm upgrade plan/apply`, kubelet/kubectl, drain/uncordon)
- [ ] **etcd** backup & restore (`etcdctl snapshot save/restore`, `--endpoints`, certs)
- [ ] Manage certificates / kubeconfig; understand static pod manifests (`/etc/kubernetes/manifests`)
- [ ] Helm & Kustomize basics for installing/managing components
- [ ] Control-plane components & their roles (apiserver, scheduler, controller-manager, kubelet, kube-proxy)
- **Deliverable focus:** upgrade a cluster; back up + restore etcd; craft least-privilege RBAC.

## 2. Workloads & Scheduling — 15%
- [ ] Deployments: rollouts, rollbacks, scaling, `kubectl set image`, revision history
- [ ] ConfigMaps & Secrets: as env vars and as mounted volumes
- [ ] Pod scheduling: nodeSelector, affinity/anti-affinity, taints & tolerations, `nodeName`
- [ ] Resource requests/limits; QoS classes; LimitRange & ResourceQuota
- [ ] Self-healing: liveness / readiness / startup probes
- [ ] DaemonSets, static pods, multi-container patterns (sidecar/init)
- **Deliverable focus:** rolling update + rollback; schedule a pod onto a tainted node.

## 3. Services & Networking — 20%
- [ ] Pod-to-pod connectivity model; CNI concepts (install a CNI plugin)
- [ ] Services: ClusterIP, NodePort, LoadBalancer, `endpoints`, selectors
- [ ] **Ingress** controllers & Ingress resources, path/host routing
- [ ] **NetworkPolicy**: default-deny, ingress/egress allow rules
- [ ] CoreDNS / cluster DNS resolution & troubleshooting
- [ ] Gateway API (aware of it), `kubectl port-forward`
- **Deliverable focus:** expose an app via Ingress; lock it down with a NetworkPolicy.

## 4. Storage — 10%
- [ ] PersistentVolumes, PersistentVolumeClaims, binding, accessModes, reclaim policy
- [ ] **StorageClasses** & dynamic provisioning; default class
- [ ] volumeMode, expansion; mounting PVCs into pods
- [ ] Volume types: emptyDir, hostPath, configMap/secret volumes
- **Deliverable focus:** dynamic PVC via StorageClass; mount + verify persistence across pod restart.

## 5. Troubleshooting — 30% (heaviest)
- [ ] Cluster component failures: kubelet down, static pod misconfig, apiserver/etcd issues
- [ ] Node problems: `NotReady`, disk/memory pressure, `journalctl -u kubelet`, `crictl`
- [ ] Workload failures: CrashLoopBackOff, ImagePullBackOff, Pending, OOMKilled, probe failures
- [ ] Networking: Service has no endpoints, DNS broken, NetworkPolicy blocking traffic
- [ ] RBAC/authorization failures (`can-i`, forbidden errors)
- [ ] Reading logs & events: `kubectl describe`, `kubectl get events --sort-by=...`, `logs -p`
- [ ] Monitor cluster & app health; interpret metrics
- **Deliverable focus:** given a broken cluster, restore it to healthy within a time limit.

---

## Exam-day skills (cross-cutting, drill daily)
- [ ] `kubectl` speed: `alias k=kubectl`, `export do='--dry-run=client -o yaml'`, `--force`
- [ ] Fast YAML: generate then edit, `kubectl explain <res>.<field>`
- [ ] Context switching: `kubectl config use-context`, `-n <ns>`, `kubens`/`kubectx`
- [ ] `vim` fluency (set number, indentation, yaml mode)
- [ ] Time management: flag/skip hard tasks, verify each answer before moving on
