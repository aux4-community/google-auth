#### Description

The `google auth` command group manages authentication for all aux4 Google Workspace packages. It provides login, status, and logout commands that wrap the Google Workspace CLI (`gws`) authentication flow.

Available subcommands:

- **login** — Authenticate with Google (opens browser)
- **status** — Show current authentication status
- **logout** — Clear saved credentials and token cache

#### Usage

```bash
aux4 google auth <subcommand>
```

#### Example

```bash
aux4 google auth login --services sheets,drive
aux4 google auth status
```
