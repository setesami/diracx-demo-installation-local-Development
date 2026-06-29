# 03 — Authentication

[Previous: Kubernetes Concepts](02-kubernetes.md) | [Back to main README](../README.md) | [Next: REST API Exploration](04-rest-api.md)

---

## Page Contents

- Swagger login
- Bearer token
- Protected APIs
- Authentication flow
- Screenshots to add

---


The DIRACX demo exposes Swagger at:

```text
https://<IP>.nip.io:8000/api/docs
```

When prompted to log in:

```text
Username: admin@example.com
Password: password
```

## Bearer token

After logging in, Swagger obtains a JWT access token.

Protected API calls include an HTTP header like:

```text
Authorization: Bearer <JWT_TOKEN>
```

This token proves that the user is authenticated.

## Authentication flow

The demo uses OpenID Connect/OAuth-style authentication.

Important endpoints observed in Swagger:

```text
/api/auth/authorize
/api/auth/token
/.well-known/openid-configuration
```

## Protected endpoint

A protected endpoint is an API that requires authentication.

For example, job submission, job search, and job status update require the Bearer token.

## Screenshots to add

Suggested screenshots:

```text
docs/screenshots/swagger-auth-login.png
docs/screenshots/swagger-authorize-modal.png
docs/screenshots/swagger-token-success.png
```

---

[Previous: Kubernetes Concepts](02-kubernetes.md) | [Back to main README](../README.md) | [Next: REST API Exploration](04-rest-api.md)
