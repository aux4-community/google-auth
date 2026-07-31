# google auth

Part of the optional `integration` group in `test.suite.md`. These tests need a
completed `aux4 google auth login` — a real Google Cloud OAuth Desktop client and
a human approving the consent screen in a browser — so they only run when asked
for explicitly:

```bash
aux4 test run --group integration
```

The `login` command itself cannot be automated: it prints an authorization URL,
waits on `http://localhost:9876/callback`, and only returns once the browser
redirect arrives. Verify it by hand and check that the printed URL carries
`access_type=offline`, `prompt=consent` and `include_granted_scopes=true`.

```timeout
20000
```

## after a successful login

### should report a valid token for the google provider

```execute
aux4 google auth status
```

```expect:partial
Provider:      google
Status:        valid
```

### should report that a refresh token was stored

```execute
aux4 google auth status
```

```expect:partial
Refresh token: yes
```

### should report the authenticated account

```execute
aux4 google auth status
```

```expect:partial
Account:       *?
```
