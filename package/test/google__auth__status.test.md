# google auth status

```timeout
15000
```

## should show authentication state

### should return auth status as JSON

```execute
aux4 google auth status
```

```expect:partial
"auth_method"
```
