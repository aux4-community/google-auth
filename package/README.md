# community/google-auth

Authentication for Google Workspace CLI packages

This package provides authentication commands for all aux4 Google Workspace packages (`community/google-sheets`, `community/google-drive`, etc.). It wraps the Google Workspace CLI (`gws`) authentication flow so users can log in, check their status, and log out with simple aux4 commands.

## Installation

```bash
aux4 aux4 pkger install community/google-auth
```

This package is also installed automatically as a dependency of other Google Workspace packages.

## System Dependencies

This package requires the Google Workspace CLI (`gws`). It will be installed automatically via one of the following:

- [brew](https://brew.sh): `brew install googleworkspace-cli`
- [npm](https://www.npmjs.com): `npm install -g @googleworkspace/cli`

## Quick Start

See available services:

```bash
aux4 google services list
```

Log in authorizing only what you need:

```bash
aux4 google auth login --services sheets
```

Check if you are authenticated:

```bash
aux4 google auth status
```

## Commands

### Login

Authenticate with Google via OAuth2. This opens your browser where you select your Google account and approve access. Credentials are stored securely in your system keyring.

Use `--services` to authorize only the services you need. You can re-run login later to add more services without losing existing access.

On first run, the default aux4 OAuth client is provisioned automatically — no Google Cloud project needed. If you prefer your own OAuth credentials, place your `client_secret.json` in `~/.config/gws/` before running login.

```bash
aux4 google auth login --services sheets,drive
```

Read-only access:

```bash
aux4 google auth login --services drive --readonly
```

### Services List

List all available Google Workspace services:

```bash
aux4 google services list
```

### Status

Show the current authentication state, including whether you have valid credentials and which project is configured.

```bash
aux4 google auth status
```

### Logout

Clear all saved credentials and token cache.

```bash
aux4 google auth logout
```

## How it works

Under the hood, this package calls the `gws auth` commands from the [Google Workspace CLI](https://github.com/googleworkspace/cli). Credentials are stored encrypted in `~/.config/gws/` using your system keyring for the encryption key.

## Environment Variables

For CI or headless environments, you can skip the browser login by setting credentials directly:

- `GOOGLE_WORKSPACE_CLI_TOKEN` — OAuth access token (highest priority)
- `GOOGLE_WORKSPACE_CLI_CREDENTIALS_FILE` — Path to a credentials or service account JSON file

## License

MIT — See [LICENSE](./license) for details.
