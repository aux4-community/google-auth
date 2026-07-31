# google auth command injection

Regression test for the shell command-injection class fixed by switching raw
`'${var}'` interpolation to the shell-escaped `param()`/`value()` functions.
The malicious `--services` value carries a single quote plus a `touch` command.
Before the fix it broke out of the `each:aux4 google ${item} services` step and
created the marker file; after the fix the value is passed as a single escaped
argument and the marker is never created.

## with a single-quote payload in --services

```beforeAll
rm -f /tmp/AUX4_INJ_auth
```

```afterAll
rm -f /tmp/AUX4_INJ_auth
```

### should not execute the injected command

The injectable service-scan step runs before any network activity, so the login
fails harmlessly (no real Google service resolves) without reaching Google.

```execute
aux4 google auth login --services "x'; touch /tmp/AUX4_INJ_auth; echo '" --clientId cid --clientSecret s --tokenFile /tmp/AUX4_INJ_token </dev/null
```

```error:partial
No Google service packages installed.
```

### should leave no injection marker behind

```execute
test -f /tmp/AUX4_INJ_auth && echo VULNERABLE || echo SAFE
```

```expect
SAFE
```
