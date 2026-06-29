# 04 — REST API Exploration

[Previous: Authentication](03-authentication.md) | [Back to main README](../README.md) | [Next: Job Submission](05-job-submission.md)

---

## Page Contents

- HTTP methods
- Job summary
- Job search
- Operators
- SQL analogy

---


This document summarizes the REST APIs explored in the DIRACX demo.

## HTTP methods

- `GET`: retrieve information
- `POST`: send data to create or query something
- `PATCH`: partially update an existing resource

## Job summary

Endpoint:

```text
POST /api/jobs/summary
```

Purpose:

Group jobs by one or more parameters.

Example request:

```json
{
  "grouping": [
    "Status"
  ],
  "search": []
}
```

Example response:

```json
[
  {
    "Status": "Received",
    "count": 1
  }
]
```

This means there is one job with status `Received`.

## Invalid grouping example

Using:

```json
{
  "grouping": [
    "string"
  ],
  "search": []
}
```

returns:

```json
{
  "detail": "Unrecognised parameters requested {'string'}"
}
```

because `string` is only a Swagger placeholder and not a real DIRACX job parameter.

## Job search

Endpoint:

```text
POST /api/jobs/search?page=1&per_page=100
```

Search by one JobID:

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

Search by multiple JobIDs:

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

## Operators

Operators define how a field is compared to a value.

| Operator | Meaning |
|----------|---------|
| `eq` | equal |
| `ne` | not equal |
| `gt` | greater than |
| `ge` | greater than or equal |
| `lt` | less than |
| `le` | less than or equal |
| `in` | one of several values |
| `not in` | not one of several values |

For `eq`, use:

```json
"value": "1"
```

For `in`, use:

```json
"values": ["1", "2"]
```

## SQL analogy

The API request:

```json
{
  "parameter": "JobID",
  "operator": "in",
  "values": ["1", "2"]
}
```

is conceptually similar to:

```sql
WHERE JobID IN (1, 2)
```

This is only an analogy. The user does not write SQL directly when using the DIRACX API.

---

[Previous: Authentication](03-authentication.md) | [Back to main README](../README.md) | [Next: Job Submission](05-job-submission.md)
