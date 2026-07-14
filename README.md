# k8s-lab

A personal Kubernetes learning lab — a collection of raw manifests (Pods, Deployments, Services, Namespaces) applied to a cluster with `kubectl`. There is no application source code or build system here; the manifests *are* the project.

## Contents

| Path | What it is |
| --- | --- |
| `lab01/` | Namespace exercise (`ns.yaml`). |
| `deployments/` | Pod & Deployment experiments: `nginx.yaml`, `nginx-doc.yaml`, `deploy.yaml` (httpd, 10 replicas), `nginx-pod.yaml` (nginx + busybox sidecar sharing an `emptyDir` volume), and `nginx-mealie.yaml` (nginx + busybox sidecar mounting the `mealie-data` PVC). |
| `mealie/` | A self-contained app stack — namespace, a `mealie-data` PersistentVolumeClaim (`storage.yaml`, 500Mi RWO), a Deployment that mounts it, and a `LoadBalancer` Service ([Mealie](https://mealie.io/) on port 9000). |
| `monitoring/` | `prometheus-default-values.yaml` — the upstream Helm values file for the `kube-prometheus-stack` chart (installed via Helm, not `kubectl apply`). |

## Usage

Apply order matters: create a namespace before the resources that reference it.

```bash
# mealie stack (namespace first, then PVC + deployment + service)
kubectl apply -f mealie/mealie-ns.yaml
kubectl apply -f mealie/

# a single resource
kubectl apply -f deployments/nginx-pod.yaml

# monitoring is a Helm values file, not a manifest
helm install kube-prometheus-stack prometheus-community/kube-prometheus-stack \
  -f monitoring/prometheus-default-values.yaml

# validate without applying
kubectl apply --dry-run=client -f <file>

# inspect / tear down
kubectl get all -n mealie
kubectl delete -f <file>
```

## Conventions

- Deployments and their Services are matched by an `app: <name>` label — keep the Deployment's pod-template labels, its `selector.matchLabels`, and the Service `selector` in sync.
- Namespaced resources (the `mealie/` stack) must keep the `namespace:` field consistent across all files.
- PVCs are referenced by `claimName`: the `mealie-data` claim is mounted by both `mealie/deployment.yaml` and `deployments/nginx-mealie.yaml`. A PVC is bound to one namespace, so a pod outside `mealie` cannot mount it.
