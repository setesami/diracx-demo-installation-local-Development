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

The DIRACX demo exposes the Swagger API documentation at:

```text
https://<IP>.nip.io:8000/api/docs
```

To authenticate, click the **Authorize** button located in the upper-right corner of the Swagger page.

The **client_id** field is already populated. Under **Scopes**, select:

* `vo:diracAdmin`
* `property:NormalUser`

Then click **Authorize**.

![Swagger Authentication](docs/screenshots/swagger-auth.png)

After clicking **Authorize**, you will be redirected to the Dex login page.

![Swagger Login](docs/screenshots/swagger-login.png)

Log in using the demo credentials:

```text
Username: admin@example.com
Password: password
```

After a successful login, click the **Grant Access** button to authorize Swagger to access the DIRACX APIs on your behalf.

![Swagger grant](docs/screenshots/swagger-grantacess.png)


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


---

[Previous: Kubernetes Concepts](02-kubernetes.md) | [Back to main README](../README.md) | [Next: REST API Exploration](04-rest-api.md)
