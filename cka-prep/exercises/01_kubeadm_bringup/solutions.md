# Solutions — 01 kubeadm bring-up & control plane

Covers `tasks_0102.md` (Day 01.02, 2026-07-20). Everything below is verified against the
`cka-prep` kind cluster, v1.36.1.

Almost all of this lives **inside the node**, not on the Mac:

```bash
# containerd (current setup, since 2026-07-22):
nerdctl --namespace k8s.io exec -it cka-prep-control-plane bash
# was, under dockerd/moby: docker exec -it cka-prep-control-plane bash
```

> **Stale environment.** This run was recorded on a kind cluster under dockerd. kind no longer works
> here — see the container-engine section in `CLAUDE.md`. The *reasoning* below still stands; the
> node-entry command does not.

---

## Task 1 — runtime and its containers, without `kubectl`

```bash
containerd --version          # binary on disk
ctr version                   # client AND server → proves the daemon is answering
crictl ps                     # CRI-level: pods and containers as Kubernetes sees them
crictl ps | grep kube         # faster when you only want the control plane
```

`ctr version` beats `containerd --version`: it round-trips to the daemon, so a running answer
proves the runtime is actually up, not merely installed.

`ctr` also lists containers, but it is **namespace-scoped** — Kubernetes containers live in the
`k8s.io` namespace, so a bare `ctr containers ls` returns empty and looks like nothing is running.
Use `ctr -n k8s.io containers ls` if you go that route. `crictl` is the CRI-level tool, understands
pods, and is what the exam expects.

**Reading the output:** the `ATTEMPT` column is the restart counter. A whole control plane at
attempt 2 while `etcd` and `kube-apiserver` sit at 0 means the *node* bounced and those two came up
in a fresh sandbox — a node-level restart, not a per-component crash.

## Task 2 — etcd from its manifest alone

```bash
grep -E 'data-dir|listen-client-urls|peer-' /etc/kubernetes/manifests/etcd.yaml
```

| What | Value |
| ---- | ----- |
| Data directory | `/var/lib/etcd` |
| Client listen URLs | `https://127.0.0.1:2379,https://172.18.0.4:2379` (port **2379**) |
| Peer cert | `/etc/kubernetes/pki/etcd/peer.crt` |
| Peer key | `/etc/kubernetes/pki/etcd/peer.key` |
| Peer CA | `/etc/kubernetes/pki/etcd/ca.crt` |

**The trap:** there are two nearly identical cert sets. `--cert-file` / `--key-file` /
`--trusted-ca-file` are *client* traffic; `--peer-cert-file` / `--peer-key-file` /
`--peer-trusted-ca-file` are *peer* (etcd-to-etcd) traffic. Both trust roots are the same
`ca.crt` here — the split is about cert purpose, not a separate CA.

Ports: **2379** clients, **2380** peers.

`/var/lib/etcd` is the directory Day 3 restores into (`etcdctl snapshot restore --data-dir=...`,
then repoint the manifest).

## Task 3 — kubelet: unit, config, staticPodPath

```bash
systemctl status kubelet      # state
systemctl cat kubelet         # unit + every drop-in, in order  ← the better command
grep '^staticPodPath:' /var/lib/kubelet/config.yaml
```

- **Unit:** `kubelet.service` (`/etc/systemd/system/kubelet.service`), `active (running)`.
- **Drop-ins:** `/etc/systemd/system/kubelet.service.d/{10-kubeadm.conf,11-kind.conf}`.
- **Config file:** `/var/lib/kubelet/config.yaml`, supplied by `10-kubeadm.conf` via
  `KUBELET_CONFIG_ARGS=--config=/var/lib/kubelet/config.yaml`.
- **`staticPodPath`:** `/etc/kubernetes/manifests` — the same directory `etcd.yaml` was read from
  in Task 2. That agreement *is* the evidence.

Prefer `systemctl cat` over `systemctl status` when hunting config: it shows the base unit and all
drop-ins stacked in override order. **On the exam, a sabotaged kubelet usually hides in the drop-in
directory** — the unit file itself looks pristine.

Note kind's `ConditionPathExists=/var/lib/kubelet/config.yaml`: delete that file and the kubelet
will not start at all.

## Task 4 — break and restore apiserver client-cert trust

```bash
# 1. ALWAYS back up first — and NOT into the manifests dir, the kubelet would try to run it
cp /etc/kubernetes/manifests/kube-apiserver.yaml /root/kube-apiserver.yaml.bk

# 2. break it: point --client-ca-file at a path that does not exist
#    - --client-ca-file=/etc/kubernetes/pki/xyz-ca.crt

# 3. observe (~20-30s for the kubelet to notice), from the NODE — kubectl is dead
crictl ps -a | grep kube-apiserver        # Exited, ATTEMPT climbing
crictl logs <container-id>

# 4. restore
cp /root/kube-apiserver.yaml.bk /etc/kubernetes/manifests/kube-apiserver.yaml

# 5. prove recovery on BOTH sides of the Mac/node line
crictl ps | grep kube-apiserver           # Running, ATTEMPT back to 0
kubectl get nodes                         # from the Mac — not closed until this answers
```

The error:

```
E0720 09:29:17.166080  1 run.go:72] "command failed"
  err="open /etc/kubernetes/pki/xyz-ca.crt: no such file or directory"
```

**Two failure shapes, same flag — know both:**

| `--client-ca-file` is… | Symptom | How you find it |
| ---------------------- | ------- | --------------- |
| A missing path | Apiserver fails fast at startup, `Exited`, clear log line | `crictl logs` |
| A valid file, wrong CA | Apiserver stays `Running`; every client gets `Unauthorized` | No crash, no log — compare the CA against `kubelet.conf` |

The second is the nasty one and is far more exam-like.

**The cascade (the real lesson):** breaking the apiserver alone restarted
`kube-controller-manager` and `kube-scheduler` too, and left the scheduler `0/1` for a minute.
They are API clients — no apiserver means no leader election, so they crash and back off, then
recover on their own. *When several control-plane components look sick, find the one the others
depend on and fix only that.*

## Task 5 — cordon, drain, uncordon

```bash
kubectl get pods -A -o wide | grep worker2     # know what you are evicting FIRST
kubectl cordon cka-prep-worker2
kubectl drain cka-prep-worker2 --ignore-daemonsets --force
kubectl get pods -A -o wide | grep worker2     # only DaemonSet pods remain
kubectl uncordon cka-prep-worker2              # return to service — do not forget this
```

A bare `kubectl drain` refuses and names both flags in the error:

```
cannot delete Pods that declare no controller (use --force to override): default/drain-victim
cannot delete DaemonSet-managed Pods (use --ignore-daemonsets to ignore): kube-system/kindnet-...
```

**The two flags mean opposite things:**

- `--force` **deletes** the uncontrolled pod. Nothing recreates it — it is gone permanently, not
  rescheduled elsewhere.
- `--ignore-daemonsets` **skips** the DaemonSet pods, leaving them running.

**Why DaemonSet pods don't leave (the scored bit):** cordon works by applying the
`node.kubernetes.io/unschedulable` taint — that taint is what makes a drain *stick*. DaemonSet
pods **tolerate that taint by design**. Evict one and the controller places it straight back on
the still-unschedulable node. Draining them isn't hard, it's *meaningless* — hence a flag that
skips them rather than one that forces them.

Cordon ≠ drain: cordon only stops *new* placement; existing pods stay until drained.

### Making a drain victim on this cluster

```bash
kubectl run drain-victim --image=registry.k8s.io/pause:3.10 \
  --image-pull-policy=IfNotPresent \
  --overrides='{"spec":{"nodeName":"cka-prep-worker2"}}'
```

`nodeName` bypasses the scheduler entirely (unlike `nodeSelector`, which only constrains it) —
which is also why such a pod lands even on a cordoned node.

`registry.k8s.io/pause` was used because that cluster could not pull from Docker Hub — a fault
**cleared on 2026-07-22** by the containerd switch (`docker.io` pulls now succeed). `pause` is already cached on every node and needs no network.

## Task 6 — why the control plane starts with no scheduler

They are **static pods**: the kubelet reads `/etc/kubernetes/manifests` (the `staticPodPath` from
Task 3) straight off local disk and starts them itself. No scheduler, no apiserver, no API call
involved — which is the only way to break the chicken-and-egg problem, since the apiserver cannot
schedule the apiserver.

**Evidence from this cluster, all of it already gathered:**

- Task 3: `staticPodPath: /etc/kubernetes/manifests` matches where the manifests live.
- Task 4: editing that file on disk caused the kubelet to restart the pod — with the apiserver
  *down*, so nothing else could have issued the instruction.
- Task 1: `crictl` showed `etcd` running while the apiserver was dead.

**`kubectl delete` on a static pod:** what exists in the API is a **mirror pod** — a read-only
representation the kubelet publishes so static pods are visible to `kubectl`. Deleting it removes
the *representation*, never the container; the kubelet re-publishes it within seconds.

To actually remove one, move the manifest out of the directory:

```bash
mv /etc/kubernetes/manifests/kube-apiserver.yaml /tmp/    # kubelet stops the container
mv /tmp/kube-apiserver.yaml /etc/kubernetes/manifests/    # and starts it again
```

That file-move is the real tool for control-plane surgery, and Day 14 leans on it.
