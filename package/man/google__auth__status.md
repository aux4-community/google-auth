#### Description

The `status` command reports on the stored Google OAuth token: whether it is still valid, which scopes it carries, when the access token expires, whether a refresh token is present, where the file lives, and which Google account it belongs to.

The account line comes from a call to Google's userinfo endpoint and is only printed when the lookup succeeds. It is omitted when the token was granted without the `email` scope, when the token can no longer be refreshed, or when there is no network — the rest of the report is still printed and the command still succeeds.

If there is no token at all the command writes `No token found for provider "google"` to stderr and exits with status 1, which makes it usable as a precondition check in scripts.

`Refresh token: yes` is the line to look at after a fresh login. Without a refresh token the access token stops working roughly an hour later; re-run `aux4 google auth login` to get one.

#### Usage

```bash
aux4 google auth status [--tokenFile <path>]
```

--tokenFile  Where the token is stored (env: `AUX4_GOOGLE_TOKEN_FILE`, default: `~/.aux4.config/.oauth/google.json`)

#### Example

```bash
aux4 google auth status
```

```text
Provider:      google
Status:        valid
Scopes:        email openid https://www.googleapis.com/auth/drive
Expires at:    2026-08-01T12:34:56Z
Refresh token: yes
Token file:    /Users/you/.aux4.config/.oauth/google.json
Account:       you@example.com
```

Check a token belonging to a second Google account:

```bash
aux4 google auth status --tokenFile ~/.aux4.config/.oauth/google-work.json
```
