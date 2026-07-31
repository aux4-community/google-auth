# community/google-auth

OAuth2 authentication for aux4 Google packages

This package provides the authentication commands used by all aux4 Google packages (`community/google-sheets`, `community/google-drive`, `community/google-analytics`, `community/google-search-console`, etc.). It discovers the installed Google packages, collects the OAuth scopes they declare, and runs the browser authorization flow through `aux4/curl`. The resulting token is stored once and shared by every Google package.

## Installation

```bash
aux4 aux4 pkger install community/google-auth
```

This package is also installed automatically as a dependency of other Google packages.

## System Dependencies

This package requires `jq`, which is installed automatically via one of the following:

- [brew](https://brew.sh): `brew install jq`
- apt / apk / yum: `jq`

## OAuth client setup

You need your own Google Cloud OAuth client. Nothing is bundled with this package: no client ID or client secret ships in the published package, so authentication always runs under your own Google Cloud project, quota, and consent screen.

1. Open the [Google Cloud Console](https://console.cloud.google.com/) and select or create a project.
2. Enable the APIs you plan to use (for example Google Drive API, Google Sheets API, Search Console API, Google Analytics Data API) under **APIs & Services → Library**.
3. Go to **APIs & Services → OAuth consent screen**. Pick **External** (or **Internal** for a Workspace organization), fill in the app name and support email, and add your own Google account under **Test users** while the app is unpublished.
4. Go to **APIs & Services → Credentials → Create credentials → OAuth client ID**.
5. Choose application type **Desktop app** and give it a name. A Desktop (installed) client is what the login flow expects: it authorizes through a loopback redirect on `http://localhost:9876/callback` and it is the client type that returns a refresh token.
6. Press **Download JSON** on the newly created client. The file contains an `installed` object with your `client_id` and `client_secret`.

There is no config file to place anywhere. Pass the two values to `aux4 google auth login`, or export them once:

```bash
export GOOGLE_CLIENT_ID="000000000000-xxxxxxxxxxxxxxxxxxxxxxxxxxxx.apps.googleusercontent.com"
export GOOGLE_CLIENT_SECRET="<your client secret>"
```

Both accept a `secret://` reference, so the secret never has to sit in your shell history or profile:

```bash
export GOOGLE_CLIENT_SECRET="secret://1password/Private/google-oauth/credential"
```

**Note:** treat the client secret as a credential. A Desktop OAuth client secret is not usable on its own — an attacker also needs a user to complete the consent screen — but it identifies your project and its quota, so keep it out of version control.

## Quick Start

See which services are available from your installed packages:

```bash
aux4 google services list
```

```text
 sheets                             https://www.googleapis.com/auth/spreadsheets
 Read and write spreadsheets

 drive                                     https://www.googleapis.com/auth/drive
 Manage files, folders, and shared drives

 search-console https://www.googleapis.com/auth/webmasters.readonly https://www.googleapis.com/auth/indexing
 Access Google Search Console data and Indexing API

 analytics                    https://www.googleapis.com/auth/analytics.readonly
 Access Google Analytics data (GA4)
```

Log in with all installed services at once:

```bash
aux4 google auth login
```

The command prints an authorization URL. Open it in a browser, pick the Google account and approve the requested access. The browser is redirected back to `http://localhost:9876/callback`, the token is written to disk, and the command reports which account was authorized.

Or authorize only specific services:

```bash
aux4 google auth login --services sheets,drive
```

Check if you are authenticated:

```bash
aux4 google auth status
```

## Commands

### Login

Authenticate with Google via OAuth2. By default, login collects scopes from all installed Google packages. Use `--services` to authorize only specific services.

```bash
# All installed services
aux4 google auth login

# Specific services only
aux4 google auth login --services analytics,search-console

# Read-only access
aux4 google auth login --readonly true

# Explicit client, no environment variables
aux4 google auth login --clientId 000000000000-xxxx.apps.googleusercontent.com --clientSecret "$SECRET"
```

Every login requests `openid` and `email` in addition to the service scopes, which is how the account address is reported:

```text
Authenticated as you@example.com
Services: analytics, drive, search-console, sheets
Token file: /Users/you/.aux4.config/.oauth/google.json
```

The authorization request always sends `access_type=offline` (so Google returns a refresh token), `prompt=consent` (so a repeat authorization returns one again) and `include_granted_scopes=true` (incremental authorization). Because of the last one, re-running login later with an extra service **adds** to what you already granted instead of replacing it.

If no installed package provides a service, login stops with an error instead of requesting an empty scope list. If no client ID is available, it stops before contacting Google.

### Read-only scopes

`--readonly true` asks each service for its read-only scope instead of its full scope. A service opts in by publishing a `readonlyScope` field in its metadata (see *Adding a new Google package*). Services that do not publish one fall back to their normal read-write scope, and login prints which ones did:

```bash
aux4 google auth login --readonly true
```

```text
These services do not declare a readonlyScope, so their full read-write scope was requested: sheets, drive
```

### Services List

List all available Google services from installed packages:

```bash
aux4 google services list
```

The list is dynamic — it discovers services from whatever Google packages you have installed. Install a new package (e.g. `community/google-analytics`) and it automatically appears in the list.

Narrow it down to specific services:

```bash
aux4 google services list --services sheets,drive
```

JSON output for scripting:

```bash
aux4 google services list --json true
```

```json
[
  {
    "name": "sheets",
    "scope": "https://www.googleapis.com/auth/spreadsheets",
    "description": "Read and write spreadsheets"
  },
  {
    "name": "drive",
    "scope": "https://www.googleapis.com/auth/drive",
    "readonlyScope": "https://www.googleapis.com/auth/drive.readonly",
    "description": "Manage files, folders, and shared drives"
  }
]
```

### Status

Show the stored token: whether it is still valid, which scopes it carries, when it expires, whether a refresh token is present, and which account it belongs to.

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

The `Account` line is omitted when the account cannot be looked up — for example when the token was granted without the `email` scope, or when there is no network.

### Logout

Remove the stored token. The next command that needs Google access will fail until you log in again.

```bash
aux4 google auth logout
```

## Where the token is stored

The token lives in a single file shared by every Google package:

```text
~/.aux4.config/.oauth/google.json
```

The path is absolute, not relative to the directory you happen to be in, so a login performed in one project keeps working everywhere. Override it with `--tokenFile` or with the `AUX4_GOOGLE_TOKEN_FILE` environment variable — for example to keep separate tokens for separate Google accounts:

```bash
AUX4_GOOGLE_TOKEN_FILE=~/.aux4.config/.oauth/google-work.json aux4 google auth login
```

**Note:** the file holds the client ID, client secret and refresh token as plain JSON with `0600` permissions. It is not encrypted. Treat it like an SSH private key: do not copy it into a repository, a container image, or a shared machine.

## How it works

Each Google package exposes a private `services` command that returns its name, description, and required OAuth scope as JSON. When you run `aux4 google auth login`, the auth package:

1. Lists the commands available under `aux4 google`
2. Asks each of them for its service metadata, skipping the ones that do not provide any
3. Collects the unique scopes, adds `openid` and `email`, and runs the browser authorization flow
4. Stores the resulting token where every Google package can find it

You never need to know scope URLs — install the packages you need and run `aux4 google auth login`.

## Adding a new Google package

If you're creating a new Google package, add a private `services` command to your package's profile:

```json
{
  "name": "services",
  "private": true,
  "execute": [
    "echo '{\"name\":\"my-service\",\"scope\":\"https://www.googleapis.com/auth/my.scope\",\"readonlyScope\":\"https://www.googleapis.com/auth/my.scope.readonly\",\"description\":\"My service description\"}'"
  ],
  "help": {
    "text": "Output service metadata for auth resolution"
  }
}
```

The metadata fields are:

| Field | Required | Meaning |
|---|---|---|
| `name` | yes | Service name used by `--services` |
| `description` | yes | One-line description shown by `aux4 google services list` |
| `scope` | yes | Scope(s) the package needs, space-separated if more than one |
| `readonlyScope` | no | Scope(s) requested instead of `scope` when `--readonly true` is used |

Declare **every** scope the package actually calls, not just its main one — a command that reaches into another API (for example creating a spreadsheet inside a Drive folder) needs that API's scope too, otherwise it fails with a 403 at run time.

The auth package discovers the command automatically.

## Environment Variables

- `GOOGLE_CLIENT_ID` — OAuth client ID of your Desktop app client
- `GOOGLE_CLIENT_SECRET` — OAuth client secret of the same client
- `AUX4_GOOGLE_TOKEN_FILE` — path of the token file (default `~/.aux4.config/.oauth/google.json`)

## License

MIT — See [LICENSE](./LICENSE) for details.
