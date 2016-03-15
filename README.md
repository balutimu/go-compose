# go-compose

[![Build Status](https://api.travis-ci.org/ibrt/go-compose.svg?branch=master)](https://travis-ci.org/ibrt/go-compose?branch=master)
[![Coverage Status](https://coveralls.io/repos/github/ibrt/go-compose/badge.svg?branch=master)](https://coveralls.io/github/ibrt/go-compose?branch=master)
[![GoDoc](https://godoc.org/github.com/ibrt/go-compose/compose?status.svg)](https://godoc.org/github.com/ibrt/go-compose/compose)

Go wrapper around Docker Compose, useful for integration testing.

Example:

```go
// Define Compose config.
var composeYML =`
test_mockserver:
  container_name: ms
  image: jamesdbloom/mockserver
  ports:
    - "10000:1080"
    - "1090"
test_postgres:
  container_name: pg
  image: postgres
  ports:
    - "5432"

// Start containers.
c, err := compose.Start(composeYML, true, true)
if err != nil {
    panic(err)
}
defer compose.Kill()

// Build MockServer public URL.
mockServerURL := fmt.Sprintf(
    "http://%v:%v",
    compose.MustInferDockerHost(),
    c.Containers["ms"].MustGetFirstPublicPort(1080, "tcp"))

// Wait for MockServer to start accepting connections.
MustConnectWithDefaults(func() error {
    _, err := http.Get(mockServerURL)
    return err
})
```
