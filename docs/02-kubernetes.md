# 02 — Kubernetes Overview for the DIRACX Demo

[Previous: Installation](01-installation.md) | [Back to main README](../README.md) | [Next: Authentication](03-authentication.md)

---

## Page Contents

- kubectl and kubeconfig
- Context
- Namespace
- Node
- Pod
- Deployment
- Service
- Ingress
- Request flow

---


This document summarizes the Kubernetes concepts encountered while running the DIRACX demo.

## Useful commands

Because the demo creates a kubeconfig file in `.demo/kube.conf`, commands should be run with:

```bash
kubectl get pods -A --kubeconfig .demo/kube.conf
```

or after setting:

```bash
export KUBECONFIG=$PWD/.demo/kube.conf
```

Then:

```bash
kubectl get pods -A
```

works directly in that terminal.

## kubeconfig

The file `.demo/kube.conf` tells `kubectl`:

- which cluster to connect to
- which user/certificate to use
- which context is current

Example structure:

```yaml
clusters:
- name: kind-diracx-demo
  cluster:
    server: https://127.0.0.1:<port>

users:
- name: kind-diracx-demo

contexts:
- name: kind-diracx-demo
  context:
    cluster: kind-diracx-demo
    user: kind-diracx-demo

current-context: kind-diracx-demo
```

## Context

A context is a named profile that connects:

```text
cluster + user + optional namespace
```

The current context decides which cluster receives `kubectl` commands.

## Namespace

A namespace organizes resources inside a cluster.

Useful command:

```bash
kubectl get namespaces --kubeconfig .demo/kube.conf
```

## Node

A node is the machine that runs workloads. In the local demo, the node is created by kind.

```bash
kubectl get nodes --kubeconfig .demo/kube.conf
```

## Pod

A pod is the smallest Kubernetes unit that runs one or more containers.

```bash
kubectl get pods -A --kubeconfig .demo/kube.conf
```

## Deployment

A deployment manages pods. It ensures that the desired number of replicas are running and recreates pods if they fail.

```bash
kubectl get deployments -A --kubeconfig .demo/kube.conf
```

## Service

A service provides a stable network endpoint for pods.

```bash
kubectl get svc -A --kubeconfig .demo/kube.conf
```

## Ingress

Ingress routes external HTTP/HTTPS traffic into services inside the cluster.

In the DIRACX demo, the URL:

```text
https://<IP>.nip.io:8000/api/docs
```

is routed through the ingress controller to the DIRACX API service.

```bash
kubectl get ingress -A --kubeconfig .demo/kube.conf
```

## Request flow

```text
Browser / curl
    ↓
Ingress controller
    ↓
Ingress rule
    ↓
Service
    ↓
Pod
    ↓
Container
    ↓
DIRACX API application
```

---

[Previous: Installation](01-installation.md) | [Back to main README](../README.md) | [Next: Authentication](03-authentication.md)
