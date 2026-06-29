# 06 — Job Management

[Previous: Job Submission](05-job-submission.md) | [Back to main README](../README.md) | [Next: Local Development Validation](07-local-development-validation.md)

---

## Page Contents

- Job search
- Job summary
- Job status update
- Timestamp behavior
- `force=true` behavior
- Screenshots to add

---


This document summarizes how jobs were searched, summarized, and updated.

## Job search

Endpoint:

```text
POST /api/jobs/search?page=1&per_page=100
```

Search for one job:

```json
{
  "search": [
    {
      "parameter": "JobID",
      "operator": "eq",
      "value": "1"
    }
  ]
}
```

Search for several jobs:

```json
{
  "search": [
    {
      "parameter": "JobID",
      "operator": "in",
      "values": [
        "1",
        "2"
      ]
    }
  ]
}
```

## Job summary

Endpoint:

```text
POST /api/jobs/summary
```

Example:

```json
{
  "grouping": [
    "Status"
  ],
  "search": []
}
```

If the response is:

```json
[
  {
    "Status": "Received",
    "count": 1
  }
]
```

this means there is one job with status `Received`.

## Job status update

Endpoint:

```text
PATCH /api/jobs/status
```

Example request body:

```json
{
  "1": {
    "2026-06-28T14:45:07Z": {
      "Status": "Running",
      "MinorStatus": "Marked as running",
      "ApplicationStatus": "Manual API test",
      "Source": "User"
    }
  }
}
```

The structure is:

```json
{
  "<JobID>": {
    "<timestamp>": {
      "Status": "...",
      "MinorStatus": "...",
      "ApplicationStatus": "...",
      "Source": "..."
    }
  }
}
```

## Timestamp behavior

The timestamp records the time of the status event.

It does **not** schedule a future status change.

If a future timestamp is provided, the API still applies the update immediately and records the status event with the provided timestamp.

## `force=true`

Observation:

- With `force=false`, `MinorStatus` and `ApplicationStatus` may update while the main `Status` remains unchanged.
- With `force=true`, the main status can be changed immediately.

This suggests that normal status-transition rules may prevent direct transitions such as:

```text
Received → Running
```

unless forced.

## Suggested screenshots

```text
docs/screenshots/job-search.png
docs/screenshots/job-summary-status.png
docs/screenshots/job-status-patch.png
docs/screenshots/job-details-after-status-update.png
```

---

[Previous: Job Submission](05-job-submission.md) | [Back to main README](../README.md) | [Next: Local Development Validation](07-local-development-validation.md)
