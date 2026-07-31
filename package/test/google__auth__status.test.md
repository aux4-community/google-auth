# google auth status

## without a stored token

### should report that the provider has no token

```execute
aux4 google auth status --tokenFile ./no-such-directory/google.json
```

```error:partial
No token found for provider "google"
```

### should read the token location from AUX4_GOOGLE_TOKEN_FILE

```execute
AUX4_GOOGLE_TOKEN_FILE=./another-missing-directory/google.json aux4 google auth status
```

```error:partial
No token found for provider "google"
```
