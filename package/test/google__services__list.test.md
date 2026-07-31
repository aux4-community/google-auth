# google services list

## with JSON output

### should return a JSON array

```execute
aux4 google services list --json true
```

```expect:regex
^\[.*\]$
```

## with a service filter

### should return an empty array when no installed package provides the service

```execute
aux4 google services list --services not-a-google-service --json true
```

```expect
[]
```

## with an empty result

### should print nothing when no service matches

```execute
aux4 google services list --services not-a-google-service
```

```expect

```
