# Execution Context: Go

**Language Rules (Go):**
- Error handling: wrap errors with call-site context, `fmt.Errorf("loading config: %w", err)`, and never discard an error with `_` unless justified inline.
- Formatting/linting: code must pass `gofmt` and `go vet`; run `golangci-lint run` if configured.
- Testing: table-driven tests with `testing.T`; run `go test ./... -race` and expect it to pass with no data races.
- Concurrency: every goroutine has a defined exit path; use `context.Context` for cancellation; protect shared state with mutexes or channels.
- Dependencies: keep `go.mod`/`go.sum` tidy (`go mod tidy`); avoid adding a dependency for something the stdlib already does.
- Build: `go build ./...` must succeed before a task is marked complete.
