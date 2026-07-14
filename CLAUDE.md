# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

A personal Kubernetes learning lab: a collection of raw manifests (Pods, Deployments, Services, Namespaces). There is no application source code, build system, or CI — the "code" is the YAML applied against a cluster with `kubectl`.

## Structure

- `lab01/` — namespace exercise (`ns.yaml`).
- `deployments/` — Pod and Deployment experiments (`nginx*.yaml`, `deploy.yaml`, `nginx-pod.yaml` with an emptyDir volume shared between nginx + busybox sidecar; `nginx-mealie.yaml` mounts the `mealie-data` PVC from the `mealie/` stack via a nginx+busybox sidecar pair). These are cluster-scoped / default-namespace resources, not tied to a specific namespace — but `nginx-mealie.yaml` depends on the `mealie` namespace's PVC.
- `mealie/` — a self-contained app: namespace (`mealie-ns.yaml`), `storage.yaml` (a `mealie-data` PersistentVolumeClaim, 500Mi RWO), `deployment.yaml` (mounts the `mealie-data` claim), and `service.yaml` (LoadBalancer on port 9000). All carry `namespace: mealie` and are wired together by the `app: mealie` label/selector and the shared `mealie-data` claim name.
- `monitoring/` — `prometheus-default-values.yaml`: the upstream Helm values file for the `kube-prometheus-stack` chart (not a manifest — install via Helm, not `kubectl apply`).

## Working with manifests

Apply order matters: create a namespace before the resources that reference it.

The `monitoring/` file is a Helm values file, not a manifest — it is consumed by `helm install ... -f monitoring/prometheus-default-values.yaml`, never `kubectl apply`.

```bash
# mealie stack (namespace first, then PVC, then workload)
kubectl apply -f mealie/mealie-ns.yaml
kubectl apply -f mealie/          # storage (PVC) + deployment + service

# monitoring (Helm, not kubectl)
helm install kube-prometheus-stack prometheus-community/kube-prometheus-stack \
  -f monitoring/prometheus-default-values.yaml

# single resource
kubectl apply -f deployments/nginx-pod.yaml

# validate without applying
kubectl apply --dry-run=client -f <file>

# inspect / tear down
kubectl get all -n mealie
kubectl delete -f <file>
```

## Conventions

- Deployments and their Services are matched by an `app: <name>` label; keep the Deployment's `template.metadata.labels`, its `selector.matchLabels`, and the Service `selector` in sync when editing.
- Namespaced resources (the `mealie/` stack) must keep the `namespace:` field consistent across all files.
- PVCs are referenced by `claimName`, not label: the `mealie-data` claim (`mealie/storage.yaml`) is mounted by both `mealie/deployment.yaml` and `deployments/nginx-mealie.yaml`. Keep the claim name in sync, and note a PVC is bound to one namespace — a pod in a different namespace cannot mount it.
