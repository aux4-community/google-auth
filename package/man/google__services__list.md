#### Description

The `list` command displays the Google services provided by the installed Google packages, together with the OAuth scope each one needs. Use the service names with `aux4 google auth login --services` to authorize only the services you need.

The list is discovered at run time: every command under `aux4 google` that publishes service metadata shows up here. Install another Google package and it appears automatically; no package is hard-coded.

- **--services** narrows the output to the named services. Names that no installed package provides are silently skipped, so an unknown name yields an empty result rather than an error.
- **--json** emits the raw service records as a JSON array, which is what `aux4 google auth login` consumes internally.

A service record carries `name`, `description` and `scope`, and optionally `readonlyScope`. `scope` may hold more than one scope, separated by spaces. `readonlyScope` is what `aux4 google auth login --readonly true` requests in place of `scope`; a service without it falls back to its full read-write scope.

#### Usage

```bash
aux4 google services list [--services <services>] [--json <true|false>]
```

--services  Comma-separated services to list (default: all installed)
--json      Output the raw service records as a JSON array (default: `false`)

#### Example

```bash
aux4 google services list
```

```text
 sheets                             https://www.googleapis.com/auth/spreadsheets
 Read and write spreadsheets

 drive                                     https://www.googleapis.com/auth/drive
 Manage files, folders, and shared drives
```

Only the services you care about, as JSON:

```bash
aux4 google services list --services sheets,drive --json true
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

Then authorize only what you need:

```bash
aux4 google auth login --services sheets,drive
```
