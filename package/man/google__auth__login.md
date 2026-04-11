#### Description

The `login` command authenticates with Google via OAuth2. It opens your default browser where you select a Google account and approve the requested permissions. After approval, credentials are stored encrypted in `~/.config/gws/` using your system keyring.

Use `--services` to authorize only the services you need. Run `aux4 google services list` to see available services. You can re-run login later to add more services without losing existing access.

On first run, if no OAuth credentials exist at `~/.config/gws/client_secret.json`, the command automatically provisions the default aux4 OAuth client. Users who prefer their own Google Cloud credentials can place their `client_secret.json` in `~/.config/gws/` before running login — it will not be overwritten.

#### Usage

```bash
aux4 google auth login [--services <services>] [--readonly]
```

--services  Comma-separated services to authorize (e.g. sheets,drive)
--readonly  Request read-only scopes

#### Example

Authorize only Sheets:

```bash
aux4 google auth login --services sheets
```

Authorize Sheets and Drive:

```bash
aux4 google auth login --services sheets,drive
```

Read-only access to Drive:

```bash
aux4 google auth login --services drive --readonly
```
