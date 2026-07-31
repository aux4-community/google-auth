#### Description

The `google auth` command group manages the OAuth2 credentials shared by every aux4 Google package. It provides login, status, and logout commands built on `aux4/curl`'s OAuth2 client.

Authentication uses your own Google Cloud OAuth client of type **Desktop app**. The client ID and secret are supplied per invocation with `--clientId` / `--clientSecret`, or once through the `GOOGLE_CLIENT_ID` / `GOOGLE_CLIENT_SECRET` environment variables (a `secret://` reference is accepted). See the README section *OAuth client setup* for how to create the client.

The resulting token is stored in `~/.aux4.config/.oauth/google.json` under the provider name `google`, which is the single store every Google package reads. Override the location with `--tokenFile` or `AUX4_GOOGLE_TOKEN_FILE`.

Available subcommands:

- **login** — Authorize in the browser and store the token
- **status** — Show the stored token and the account it belongs to
- **logout** — Remove the stored token

#### Usage

```bash
aux4 google auth <subcommand>
```

#### Example

```bash
aux4 google auth login --services sheets,drive
aux4 google auth status
```
