#### Description

The `logout` command deletes the stored Google OAuth token file. Every aux4 Google package shares that one token, so after logging out they all lose access until `aux4 google auth login` is run again.

Only the local token is removed; the authorization itself stays granted in your Google account. Revoke it at [Google Account → Third-party access](https://myaccount.google.com/connections) if you want the grant gone as well.

Logging out when there is no token is not an error: the command reports `No token found for provider "google"` and exits successfully, so it is safe to run unconditionally in cleanup scripts.

Use `--tokenFile` (or `AUX4_GOOGLE_TOKEN_FILE`) to remove a token stored somewhere other than the default location.

#### Usage

```bash
aux4 google auth logout [--tokenFile <path>]
```

--tokenFile  Where the token is stored (env: `AUX4_GOOGLE_TOKEN_FILE`, default: `~/.aux4.config/.oauth/google.json`)

#### Example

```bash
aux4 google auth logout
```

```text
Logged out from google
```

Remove the token of a second Google account:

```bash
aux4 google auth logout --tokenFile ~/.aux4.config/.oauth/google-work.json
```
