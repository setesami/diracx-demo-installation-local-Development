# 07 — Local Development Validation

[Previous: Job Management](06-job-management.md) | [Back to main README](../README.md)

---

## Page Contents

- Objective
- Verified so far
- Screenshots requested
- Web UI validation
- `dirac-client` validation
- Current conclusion

---


This document summarizes the current progress toward validating the DIRACX demo as a local development environment.

## Objective

The goal is to determine whether the local DIRACX demo is suitable for development work, especially around:

- infrastructure
- authentication
- job submission
- job inspection
- REST APIs
- Web UI
- `dirac-client`

## Verified so far

The following functionality has been verified:

- The demo can be installed locally on macOS.
- Kubernetes cluster is created using kind.
- Pods become ready.
- Swagger UI is accessible.
- Authentication works.
- Bearer token is generated.
- Jobs can be submitted through Swagger.
- Jobs can be searched by JobID.
- Jobs can be summarized by fields such as `Status`.
- Job status can be modified using the PATCH API.
- Grafana dashboard is accessible.

## Screenshots requested

The following screenshots should be added:

```text
docs/screenshots/swagger-auth.png
docs/screenshots/swagger-jobs-summary.png
docs/screenshots/swagger-submit-jdl.png
docs/screenshots/swagger-job-search.png
docs/screenshots/swagger-job-status-update.png
docs/screenshots/web-ui-login.png
docs/screenshots/web-ui-jobs-list.png
docs/screenshots/web-ui-job-details.png
docs/screenshots/grafana-dashboard.png
docs/screenshots/kubernetes-pods.png
```

## Web UI validation

To complete:

1. Open the Web UI.
2. Log in.
3. Navigate to the jobs page.
4. Submit a job if supported.
5. Open the submitted job details.
6. Capture screenshots.

## dirac-client validation

To complete:

1. Configure `dirac-client`.
2. Submit a job from the command line.
3. Record the JobID.
4. Search for the job through the REST API.
5. Confirm it appears in the Web UI.
6. Compare metadata with jobs submitted from Swagger.

## Current conclusion

The local DIRACX demo is already useful for learning and testing:

- Kubernetes infrastructure
- API access
- authentication
- job submission
- job search
- job status updates

The remaining validation is needed to determine whether it is sufficient for day-to-day local development involving the Web UI and `dirac-client`.

---

[Previous: Job Management](06-job-management.md) | [Back to main README](../README.md)
