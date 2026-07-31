# google auth logout

## without a stored token

### should say there is nothing to remove and succeed

```execute
aux4 google auth logout --tokenFile ./no-such-directory/google.json
```

```error:partial
No token found for provider "google"
```

## with a stored token

```beforeAll
mkdir -p logout-fixture
```

```afterAll
rm -rf logout-fixture
```

```file:logout-fixture/google.json
{
  "clientId": "test-client.apps.googleusercontent.com",
  "clientSecret": "",
  "authUrl": "https://accounts.google.com/o/oauth2/v2/auth",
  "tokenUrl": "https://oauth2.googleapis.com/token",
  "scopes": "openid email",
  "accessToken": "not-a-real-token",
  "refreshToken": "",
  "expiresAt": "2020-01-01T00:00:00Z"
}
```

### should remove the token file

```execute
aux4 google auth logout --tokenFile ./logout-fixture/google.json
```

```error:partial
Logged out from google
```
