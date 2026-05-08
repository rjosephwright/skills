---
name: go-programmer
description: Go-specific programming idioms -- line-length discipline (100 chars), allocation awareness (avoid but don't contort), `any` over `interface{}`, testify for assertions, cobra for CLI, error handling patterns, table-driven tests, and go tooling. Use for ANY Go coding task -- adding features, fixing bugs, writing tests, refactoring, CLI tools. Composes on the `programmer` skill; does not duplicate its content.
---

# Go programmer

Before writing Go code, read the `programmer` skill and the `git-commit` skill.
This skill covers what is unique to Go and assumes both are loaded.

## 1. Formatting and line length

Run code through `go fmt` (or `gofumpt` if the project uses it). Run `go vet`
after changes that introduce new packages or could trip static analysis.

Break lines that exceed 100 characters. Do not reflow lines that are already
under 100 characters -- only break when the line is too long. When breaking a
long line, start the continuation at a logical point (after a comma, after an
operator, or at a nested expression boundary), indented with a single tab.

Why 100: it fits side-by-side diffs and narrow terminals without forcing soft
wrap, and it is the convention in the Go standard library.

## 2. Naming

Follow Go conventions:
- `CamelCase` for exported names, `camelCase` for unexported.
- Acronyms are all-caps or all-lowercase: `HTTPServer`, `urlPath` -- never
  `HttpServer` or `URLPath`.
- Package names are lowercase, single word, no underscores: `auth`, `store`,
  `ratelimit`. Avoid `util`, `common`, `helper` -- name them for what they
  contain.
- Single-method interfaces end in `-er`: `Reader`, `Writer`, `Closer`.
- Getters drop the `Get` prefix: `Count()` not `GetCount()`. Setters keep `Set`.

## 3. `any` over `interface{}`

Always use `any` instead of `interface{}`. `any` is a built-in alias introduced
in Go 1.18 and is the idiomatic form in all modern Go code.

## 4. Error handling

Handle errors immediately after the call that produced them. Do not assign the
error to a variable and check it several lines later.

When wrapping errors up the call chain, use `fmt.Errorf("...: %w", err)` so
callers can use `errors.Is` and `errors.As`. Use a short description of what
failed: `"publish message: %w"`.

Do not log and return the same error -- either handle it (log) or propagate it
(return). The caller decides whether to log.

### Sentinel errors

Sentinel errors are for conditions that are meaningful to callers *without*
additional context. `io.EOF` is the canonical example -- the caller already
knows what it was reading, so the sentinel alone tells the full story. For most
functions, the caller needs context (which file, which key, which record), so
wrapping the underlying error with `%w` is more useful than a bespoke sentinel.
Callers can still use `errors.Is(err, fs.ErrNotExist)` on the wrapped error.

Only declare a sentinel at package level when callers genuinely need to branch
on this specific error condition and the error carries its own context:

```go
var ErrAborted = errors.New("operation aborted by user")
```

When in doubt, wrap the error; don't declare a sentinel.

## 5. Tests

Use `stretchr/testify` for assertions:
- `assert` for checks where the test can continue after failure.
- `require` for checks where the test cannot meaningfully continue (e.g., a nil
  value that the rest of the test dereferences).

Prefer table-driven tests:

```go
func TestFoo(t *testing.T) {
    tests := []struct {
        name     string
        input    T
        expected T
    }{
        {name: "golden path", input: ..., expected: ...},
        {name: "empty input",  input: ..., expected: ...},
    }
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got := Foo(tt.input)
            assert.Equal(t, tt.expected, got)
        })
    }
}
```

Put test files in the same package (not `_test` external package) unless the
test needs to exercise the public API as an external consumer.

The unimplemented skeleton idiom: `panic("unimplemented")` in the body.

Run tests with `go test ./...` from the module root, or `go test ./<pkg>` for a
specific package. Add `-race` when the code uses goroutines or channels.

## 6. CLI

Use `spf13/cobra` for CLI commands. Use `RunE` (not `Run`) so errors propagate
to the root command's error handler rather than calling `os.Exit` from leaf
commands:

```go
var rootCmd = &cobra.Command{
    Use:   "tool",
    Short: "does a thing",
    RunE: func(cmd *cobra.Command, args []string) error {
        return run()
    },
}
```

## 7. Allocations

Prefer value types and stack allocation where it is natural:
- Use value receivers unless the method needs to mutate the receiver or the
  receiver is large.
- Pass by value for small structs; pass by pointer for large structs or when
  mutation is needed.
- Pre-allocate slices with `make([]T, 0, capacity)` when the size is known.

Do not contort the code to avoid allocations unless explicitly told to do so.
Readable, correct code first; allocation parsimony is a refinement.

## 8. Tooling

Run these from the module root:
- Format: `go fmt ./...` (or `gofumpt -w .` if the project uses gofumpt).
- Vet: `go vet ./...`
- Test: `go test ./...`
- Build: `go build ./...`
- Tidy: `go mod tidy` (after adding or removing imports)
- Lint: `docker run -v ${PWD}:/code:z \
         -w /code ghcr.io/cloudboss/golangci/golangci-lint:v2.11.4-alpine \
         golangci-lint run`

## 9. Committing

Before committing, ensure the following:
- Tests must pass before commiting. This includes unit AND integration tests.
- Ensure code follows step 1. However, if code outside of the git working tree
  is changed by this, do not commit it. A separate formatting commit should be
  done to reformat the remainder of the code.
- Code should pass the linter. Only fix code in the working tree that fails the
  linter for this commit. A separate commit should fix linting issues outside
  the working tree.

To commit, use the `git-commit` skill. Do not run `git commit` directly.
