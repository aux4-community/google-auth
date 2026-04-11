#### Description

The `status` command shows the current authentication state. It reports whether credentials exist, whether the token is valid, the authentication method, and the configured project ID. This is useful for verifying that authentication is set up correctly.

#### Usage

```bash
aux4 google auth status
```

#### Example

```bash
aux4 google auth status
```

```text
{
  "auth_method": "oauth2",
  "has_refresh_token": true,
  "token_valid": true,
  "storage": "encrypted",
  "project_id": "my-project"
}
```
