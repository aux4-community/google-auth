# google auth login

These tests cover the checks that run *before* any browser or network activity,
so they are safe in CI: no OAuth client, no token file and no Google API calls
are needed. The authorization flow itself needs a human to approve consent in a
browser and is exercised by the optional `integration` group.

## without an OAuth client id

### should stop before contacting Google

```execute
GOOGLE_CLIENT_ID= aux4 google auth login --services not-a-google-service
```

```error:partial
No OAuth client id.
```

## without any installed Google service package

### should stop instead of asking Google for an empty scope list

```execute
GOOGLE_CLIENT_ID=test-client.apps.googleusercontent.com aux4 google auth login --services not-a-google-service
```

```error:partial
No Google service packages installed.
```
