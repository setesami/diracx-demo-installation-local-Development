# 01 — DIRACX Demo Installation on macOS

[Back to main README](../README.md) | [Next: Kubernetes Concepts](02-kubernetes.md)

---

## Page Contents

- Prerequisites
- Clone the repositories
- Docker Hub access issue
- macOS `base64` compatibility
- Helm timeout
- MinIO credentials
- Large image download workaround
- Successful installation
- Docker image inventory

---


This document describes my experience installing the DIRACX demo environment on a macOS laptop. The goal is to provide a practical reference for users who encounter similar issues during installation.

## Prerequisites

The following software was installed before starting:

- Docker Desktop
- Python 3.12
- Git

---

# 1. Clone the Repositories

Following the official DIRACX documentation:

```bash
git clone https://github.com/DIRACGrid/diracx.git
git clone https://github.com/DIRACGrid/diracx-charts.git

cd diracx-charts

./run_demo.sh ../diracx
```

Official documentation:

```text
https://diracx.diracgrid.org/en/latest/dev/tutorials/run-locally/
```

The installation script automatically downloads the following tools:

- kind v0.31.0
- kubectl v1.35.3
- helm v3.20.1
- yq v4.52.5

---

# 2. Issue 1: Docker Hub Access Blocked

## Problem

During installation, Docker attempted to pull:

```bash
busybox:latest
```

from Docker Hub, but Docker Hub access was blocked from my current location.

## Solution

I routed Docker traffic through a CERN SSH tunnel using Privoxy. This allowed Docker Desktop to access Docker Hub successfully.

The general idea was:

```text
Docker Desktop
    ↓
HTTP proxy through Privoxy
    ↓
SOCKS proxy through SSH tunnel
    ↓
CERN network
    ↓
Docker Hub / GHCR
```

This solved image-pulling failures caused by network restrictions.

---

# 3. Issue 2: macOS `base64` Compatibility

## Problem

The installation failed with:

```bash
base64: invalid option -- w
```

The `-w` option is available on GNU/Linux but not on macOS.

## Solution

In `run_demo.sh`, replace:

```bash
dex_admin_sub=$(printf '\n$%s\x12\x05local' "${dex_admin_uuid}" | base64 -w 0)
```

with:

```bash
dex_admin_sub=$(printf '\n$%s\x12\x05local' "${dex_admin_uuid}" | base64 | tr -d '\n')
```

## Explanation

On Linux, `base64 -w 0` disables line wrapping.

On macOS, the BSD version of `base64` does not support `-w`. The equivalent workaround is to run `base64` and then remove the newline characters using:

```bash
tr -d '\n'
```

---

# 4. Issue 3: Helm Installation Timeout

## Problem

The installation repeatedly failed with:

```text
Error: INSTALLATION FAILED: failed pre-install: 1 error occurred:
* timed out waiting for the condition
```

Investigation showed that several pods were stuck while pulling container images from GitHub Container Registry (GHCR) and Docker Hub.

## Solution

Pre-pull the required images manually so Docker can cache them locally.

```bash
docker pull ghcr.io/diracgrid/diracx/services:v0.1.0
docker pull ghcr.io/diracgrid/diracx/secret-generation:latest

docker pull docker.io/bitnamilegacy/mysql:8.0.34-debian-11-r8
docker pull busybox:latest
docker pull opensearchproject/opensearch:2.8.0
docker pull docker.io/library/redis:7.4.2-alpine
```

## Docker Hub Rate Limits

If images are pulled repeatedly, Docker Hub may return a rate-limit error.

To increase the allowed pull rate:

```bash
docker login
```

and authenticate with a Docker Hub account.

---

# 5. Issue 4: MinIO Credentials

## Problem

The default MinIO credentials did not work.

Replace:

```yaml
aws_access_key_id: console
aws_secret_access_key: console123
```

with:

```yaml
rootUser: rootuser
rootPassword: rootpass123
```

---

# 6. Issue 5: Large Image Download Failure

## Problem

The following image is relatively large:

```bash
docker pull ghcr.io/diracgrid/diracx/tasks:v0.1.0
```

Due to network limitations, I was unable to download it directly.

## Solution

Download the image on CERN LXPLUS and transfer it locally.

On LXPLUS:

```bash
docker pull ghcr.io/diracgrid/diracx/tasks:v0.1.0

docker save ghcr.io/diracgrid/diracx/tasks:v0.1.0 \
    -o diracx-tasks.tar
```

Transfer the file:

```bash
scp diracx-tasks.tar <local-machine>:
```

Load the image locally:

```bash
docker load -i diracx-tasks.tar
```

Load it into the Kind cluster:

```bash
./.demo/kind load docker-image \
    ghcr.io/diracgrid/diracx/tasks:v0.1.0 \
    --name diracx-demo
```

---

# 7. Successful Installation

After resolving the issues above, the DIRACX demo installation completed successfully.

The Swagger UI can be accessed at:

```text
https://192.168.1.4.nip.io:8000/api/docs
```

The exact IP address may be different on another machine.

Example demo output:

```text
When prompted to login the credentials are:

Username: admin@example.com
Password: password

Grafana dashboard for telemetry:

http://<IP>.nip.io:32004
```

---

# 8. Additional Notes

Most of the installation issues encountered were related to:

1. Restricted access to Docker Hub and GHCR.
2. macOS versus Linux command differences.
3. Large container image downloads.
4. Kubernetes image-pulling timeouts.

These notes may help other users complete the installation more smoothly.

---

# 9. Docker Images Required for the DIRACX Demo

The following container images are required or were pulled during installation and execution of the DIRACX demo.

They were obtained using:

```bash
docker exec diracx-demo-control-plane crictl images
```

| Registry/Image | Tag | Image ID | Size |
|----------------|-----|----------|------|
| `docker.io/bitnamilegacy/mysql` | `8.0.34-debian-11-r8` | `5fa68fc7fba69` | 212 MB |
| `docker.io/kindest/kindnetd` | `v20250512-df8de77b` | `b1a8c6f707935` | 40.6 MB |
| `docker.io/kindest/local-path-helper` | `v20241212-8ac705d0` | `b530d82c16da9` | 2.95 MB |
| `docker.io/kindest/local-path-provisioner` | `v20250214-acbabc1a` | `653e2348b2d4a` | 20.2 MB |
| `docker.io/library/busybox` | `latest` | `e0e8b3cbfed68` | 1.93 MB |
| `docker.io/library/redis` | `7.4.2-alpine` | `dc11e3ccde3c4` | 17.7 MB |
| `docker.io/opensearchproject/opensearch` | `2.8.0` | `78db6166bc58c` | 813 MB |
| `ghcr.io/dexidp/dex` | `v2.41.1` | `afe4ef0e63b41` | 36.2 MB |
| `ghcr.io/diracgrid/diracx-web/static` | `v0.1.0-a11` | `9b0cfbd805335` | 25.9 MB |
| `ghcr.io/diracgrid/diracx/client` | `v0.1.0` | `a0dea901472f8` | 721 MB |
| `ghcr.io/diracgrid/diracx/secret-generation` | `latest` | `1c01728c7b879` | 34.9 MB |
| `ghcr.io/diracgrid/diracx/services` | `v0.1.0` | `38ada00cc31e2` | 737 MB |
| `ghcr.io/diracgrid/diracx/tasks` | `v0.1.0` | `e94e27e13678e` | 735 MB |
| `quay.io/jetstack/cert-manager-cainjector` | `v1.13.1` | `5bda9bf7fd5ce` | 12.1 MB |
| `quay.io/jetstack/cert-manager-controller` | `v1.13.1` | `30e91ee7ba767` | 17.4 MB |
| `quay.io/jetstack/cert-manager-ctl` | `v1.13.1` | `f6006af04fbc8` | 16.4 MB |
| `quay.io/jetstack/cert-manager-webhook` | `v1.13.1` | `9bfab9ccea264` | 13.7 MB |
| `quay.io/minio/mc` | `RELEASE.2023-06-19T19-31-19Z` | `b7d4b2e3af421` | 101 MB |
| `quay.io/minio/minio` | `RELEASE.2023-06-19T19-52-50Z` | `2727917de685c` | 101 MB |
| `registry.k8s.io/coredns/coredns` | `v1.12.1` | `138784d87c9c5` | 20.4 MB |
| `registry.k8s.io/ingress-nginx/kube-webhook-certgen` | `<none>` | `53a0bbd6100e8` | 24.3 MB |
| `registry.k8s.io/etcd` | `3.6.4-0` | `a1894772a478e` | 98.2 MB |
| `registry.k8s.io/ingress-nginx/controller` | `<none>` | `fd161fb3d80d4` | 110 MB |
| `registry.k8s.io/kube-apiserver` | `v1.34.0` | `d291939e99406` | 24.6 MB |
| `registry.k8s.io/kube-controller-manager` | `v1.34.0` | `996be7e86d9b3` | 20.7 MB |
| `registry.k8s.io/kube-proxy` | `v1.34.0` | `6fc32d66c1411` | 22.8 MB |
| `registry.k8s.io/kube-scheduler` | `v1.34.0` | `a25f5ef9c34c3` | 15.8 MB |
| `registry.k8s.io/pause` | `3.10` | `afb61768ce381` | 268 kB |

---

[Back to main README](../README.md) | [Next: Kubernetes Concepts](02-kubernetes.md)
