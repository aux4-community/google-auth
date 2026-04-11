#### Description

The `list` command displays all available Google Workspace services. Use the service names with `aux4 google auth login --services` to authorize only the services you need.

#### Usage

```bash
aux4 google services list
```

#### Example

```bash
aux4 google services list
```

```text
drive          Manage files, folders, and shared drives
sheets         Read and write spreadsheets
gmail          Send, read, and manage email
calendar       Manage calendars and events
docs           Read and write Google Docs
```

Then authorize only what you need:

```bash
aux4 google auth login --services sheets,drive
```
