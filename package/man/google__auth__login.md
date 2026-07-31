#### Description

The `login` command authorizes aux4 against Google via OAuth2 and stores the resulting token. It prints an authorization URL, waits for a loopback redirect on `http://localhost:9876/callback`, exchanges the authorization code for a token, and writes it to `~/.aux4.config/.oauth/google.json`.

Open the printed URL in a browser, choose a Google account and approve the requested access. The command finishes on its own once the browser is redirected back — nothing needs to be pasted into the terminal.

Before the first login you need your own Google Cloud OAuth client of type **Desktop app**. Its client ID and secret come from `--clientId` / `--clientSecret`, or from the `GOOGLE_CLIENT_ID` / `GOOGLE_CLIENT_SECRET` environment variables; both accept a `secret://` reference. Nothing is bundled with the package, and login stops before contacting Google if no client ID is available. See the README section *OAuth client setup* for the Google Cloud Console steps.

- **Dynamic scopes** — the requested scopes are discovered from the installed Google packages: every package under `aux4 google` that publishes service metadata contributes its scope. Use `--services` to authorize only some of them and `aux4 google services list` to see what is available. If no installed package provides any service, login stops with an error instead of asking Google for an empty scope list.
- **Account identification** — `openid` and `email` are always requested on top of the service scopes, which is how the command reports the authorized account.
- **Refresh tokens** — the authorization request sends `access_type=offline` and `prompt=consent`, so Google returns a refresh token on the first authorization *and* on every repeat authorization. Without a refresh token, access would silently stop working about an hour later.
- **Incremental authorization** — the request also sends `include_granted_scopes=true`, so re-running login later with an additional service adds to what you already granted instead of replacing it.
- **Read-only scopes** — `--readonly true` requests each service's `readonlyScope` instead of its `scope`. Services that do not publish a `readonlyScope` fall back to their full read-write scope, and the command prints which ones did.
- **Token location** — `--tokenFile` (or `AUX4_GOOGLE_TOKEN_FILE`) chooses where the token is written. The default is absolute, so a login done in one directory works from every other directory. Point it elsewhere to keep tokens for more than one Google account.

#### Usage

```bash
aux4 google auth login [--services <services>] [--readonly <true|false>] [--clientId <id>] [--clientSecret <secret>] [--tokenFile <path>]
```

--services      Comma-separated services to authorize, e.g. `sheets,drive` (default: all installed)
--readonly      Request read-only scopes where the service declares one (default: `false`)
--clientId      Google Cloud OAuth client id of a Desktop app client (env: `GOOGLE_CLIENT_ID`)
--clientSecret  Client secret of the same client (env: `GOOGLE_CLIENT_SECRET`, default: empty)
--tokenFile     Where the token is stored (env: `AUX4_GOOGLE_TOKEN_FILE`, default: `~/.aux4.config/.oauth/google.json`)

#### Example

Authorize every installed service:

```bash
export GOOGLE_CLIENT_ID=000000000000-xxxx.apps.googleusercontent.com
export GOOGLE_CLIENT_SECRET=secret://1password/Private/google-oauth/credential
aux4 google auth login
```

```text
Open this URL in your browser to authorize:

https://accounts.google.com/o/oauth2/v2/auth?access_type=offline&prompt=consent&include_granted_scopes=true&client_id=...

Waiting for callback on port 9876...
Authenticated as you@example.com
Services: sheets, drive, search-console, analytics
Token file: /Users/you/.aux4.config/.oauth/google.json
```

Authorize Sheets and Drive only:

```bash
aux4 google auth login --services sheets,drive
```

Read-only access to Drive:

```bash
aux4 google auth login --services drive --readonly true
```

```text
These services do not declare a readonlyScope, so their full read-write scope was requested: drive
```

Keep a second token for a different Google account:

```bash
aux4 google auth login --tokenFile ~/.aux4.config/.oauth/google-work.json
```
