# Note 01 — kubeadm cluster bring-up + control-plane components (Domain 1, weight 25%)

## Concept
A kubeadm cluster has a control-plane node running four core components as **static pods**
(managed directly by the kubelet from `/etc/kubernetes/manifests/`, not by the scheduler):
- **kube-apiserver** — front door; all reads/writes go through it; talks to etcd.
- **etcd** — the cluster's key-value store; the only place state is persisted.
- **kube-controller-manager** — runs control loops (node, replication, endpoints, etc.) that
  reconcile actual state → desired state.
- **kube-scheduler** — watches for unscheduled Pods and binds them to a Node.

Worker nodes run:
- **kubelet** — the node agent; not a pod, runs as a systemd service; talks to the apiserver,
  starts/stops containers via the container runtime (containerd/CRI-O).
- **kube-proxy** — maintains Service iptables/IPVS rules on each node (runs as a DaemonSet).
- **CNI plugin** — pod networking (also a DaemonSet, e.g. kindnet/Calico/Flannel).

`kubeadm init` on the first control-plane node:
1. Runs preflight checks.
2. Generates certs (CA + component certs) into `/etc/kubernetes/pki/`.
3. Writes static pod manifests for apiserver/controller-manager/scheduler/etcd.
4. Writes `/etc/kubernetes/admin.conf` (cluster-admin kubeconfig) and kubelet kubeconfigs.
5. Starts the kubelet, which picks up the static pod manifests and starts the control plane.
6. Prints a `kubeadm join` command with a bootstrap token + CA cert hash for workers.

`kubeadm join` on a worker/second control-plane node uses that token to TLS-bootstrap:
requests a kubelet client cert from the apiserver, then the kubelet registers the Node object.

Tokens expire (24h default). Regenerate with:
```bash
kubeadm token create --print-join-command
```

## Key commands
```bash
# init a control-plane node, pick a pod CIDR matching your CNI choice
kubeadm init --pod-network-cidr=10.244.0.0/16

# on a worker, join using the token+hash printed by init (or regenerated)
kubeadm join <apiserver-host>:6443 --token <token> \
  --discovery-token-ca-cert-hash sha256:<hash>

# copy admin kubeconfig for kubectl access (as the init'ing user)
mkdir -p $HOME/.kube && cp /etc/kubernetes/admin.conf $HOME/.kube/config

# inspect static pod manifests (source of truth for control-plane config)
ls /etc/kubernetes/manifests/
cat /etc/kubernetes/manifests/kube-apiserver.yaml

# verify
kubectl get nodes -o wide
kubectl get pods -n kube-system
kubectl cluster-info
```

## Exam-legal doc path
kubernetes.io → Setup → Production Environment → **Bootstrapping clusters with kubeadm** →
"Creating a cluster with kubeadm" (has the exact init/join sequence to copy-paste).

## Gotchas / common mistakes
- Editing a static pod manifest and expecting `kubectl edit` to work — it won't; static pods
  are only editable by changing the YAML file on that node's disk (kubelet watches the dir and
  restarts the pod automatically).
- Forgetting `--pod-network-cidr` at init time when the chosen CNI requires a specific range —
  CoreDNS pods stay `Pending`/`ContainerCreating` until a CNI is installed.
- Not exporting `KUBECONFIG` — commands silently fail against the wrong (or no) cluster.
- kind cluster nodes are containers, not VMs — `kubeadm init/join` isn't run by you (kind's
  tooling does it), so kind is good for **studying** static pod manifests and components but not
  for **drilling** the actual init/join command sequence — that needs real VMs/hosts.

## Self-check questions
1. Which control-plane component would you check first if `kubectl get pods` hangs with no response?
2. Where do static pod manifests live, and how do you apply a change to one?
3. What two pieces of information does a worker need to join a cluster via `kubeadm join`?
4. Why does CoreDNS stay Pending right after `kubeadm init` with no further action?
