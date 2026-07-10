# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

A personal Kubernetes learning lab: a collection of raw manifests (Pods, Deployments, Services, Namespaces). There is no application source code, build system, or CI — the "code" is the YAML applied against a cluster with `kubectl`.

## Structure

- `lab01/` — namespace exercise (`ns.yaml`).
- `deployments/` — Pod and Deployment experiments (`nginx*.yaml`, `deploy.yaml`, `nginx-pod.yaml` with an emptyDir volume shared between nginx + busybox sidecar). These are cluster-scoped / default-namespace resources, not tied to a specific namespace.
- `mealie/` — a self-contained app: namespace (`mealie-ns.yaml`), `deployment.yaml`, and `service.yaml` (LoadBalancer on port 9000). All three carry `namespace: mealie` and are wired together by the `app: mealie` label/selector.

## Working with manifests

Apply order matters: create a namespace before the resources that reference it.

```bash
# mealie stack (namespace first)
kubectl apply -f mealie/mealie-ns.yaml
kubectl apply -f mealie/          # deployment + service

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
