# community/google-auth

Authentication for aux4 Google packages

This package provides authentication commands for all aux4 Google packages (`community/google-sheets`, `community/google-drive`, `community/google-analytics`, `community/google-search-console`, etc.). It automatically discovers installed Google packages, collects the OAuth scopes they need, and handles the login flow through the Google Workspace CLI (`gws`).

## Installation

```bash
aux4 aux4 pkger install community/google-auth
```

This package is also installed automatically as a dependency of other Google packages.

## System Dependencies

This package requires the Google Workspace CLI (`gws`). It will be installed automatically via one of the following:

- [brew](https://brew.sh): `brew install googleworkspace-cli`
- [npm](https://www.npmjs.com): `npm install -g @googleworkspace/cli`

## Quick Start

See which services are available from your installed packages:

```bash
aux4 google services list
```

```text
analytics
  Access Google Analytics data (GA4)
  scope: https://www.googleapis.com/auth/analytics.readonly

drive
  Manage files, folders, and shared drives
  scope: https://www.googleapis.com/auth/drive

search-console
  Access Google Search Console data
  scope: https://www.googleapis.com/auth/webmasters.readonly

sheets
  Read and write spreadsheets
  scope: https://www.googleapis.com/auth/spreadsheets
```

Log in with all installed services at once:

```bash
aux4 google auth login
```

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

Authenticate with Google via OAuth2. This opens your browser where you select your Google account and approve access. Credentials are stored securely in your system keyring.

By default, login collects scopes from all installed Google packages. Use `--services` to authorize only specific services.

```bash
# All installed services
aux4 google auth login

# Specific services only
aux4 google auth login --services analytics,search-console

# Read-only access
aux4 google auth login --readonly
```

After login, the output shows which account and services were authorized:

```text
Authenticated as you@example.com
Services: analytics, drive, search-console, sheets
```

On first run, the default aux4 OAuth client is provisioned automatically — no Google Cloud project needed. If you prefer your own OAuth credentials, place your `client_secret.json` in `~/.config/gws/` before running login.

### Services List

List all available Google services from installed packages:

```bash
aux4 google services list
```

The list is dynamic — it discovers services from whatever Google packages you have installed. Install a new package (e.g. `community/google-analytics`) and it automatically appears in the list.

JSON output for scripting:

```bash
aux4 google services list --json true
```

### Login from an AI Agent

When an AI agent needs to authenticate, it can't open a browser interactively. Use `--tee true` to redirect the login URL to stdout so the agent can capture it:

```bash
# Start login as a background job
aux4 jobs run "aux4 google auth login --tee true"

# Read the job output to get the login URL
aux4 jobs output <job-id>

# Give the URL to the user. The job stays running until they complete the OAuth flow.
# After the user confirms, retry the original Google command.
```

### Status

Show the current authentication state, including whether you have valid credentials and which scopes are authorized.

```bash
aux4 google auth status
```

### Logout

Clear all saved credentials and token cache.

```bash
aux4 google auth logout
```

## How it works

Each Google package exposes a private `services` command that returns its name and required OAuth scope as JSON. When you run `aux4 google auth login`, the auth package:

1. Calls `aux4 google --json` to discover installed subcommands
2. Calls each package's `services` command to collect scope URLs
3. Passes all scopes to `gws auth login --scopes`

This means you never need to know scope URLs — just install the packages you need and run `aux4 google auth login`.

## Adding a new Google package

If you're creating a new Google package, add a private `services` command to your package's profile:

```json
{
  "name": "services",
  "private": true,
  "execute": [
    "echo '{\"name\":\"my-service\",\"scope\":\"https://www.googleapis.com/auth/my.scope\",\"description\":\"My service description\"}'"
  ],
  "help": {
    "text": "Output service metadata for auth resolution"
  }
}
```

The auth package will automatically discover it.

## Environment Variables

For CI or headless environments, you can skip the browser login by setting credentials directly:

- `GOOGLE_WORKSPACE_CLI_TOKEN` — OAuth access token (highest priority)
- `GOOGLE_WORKSPACE_CLI_CREDENTIALS_FILE` — Path to a credentials or service account JSON file

## License

MIT — See [LICENSE](./LICENSE) for details.
