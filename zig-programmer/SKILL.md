---
name: zig-programmer
description: Zig-specific programming idioms -- line-length discipline (100 chars), allocator hygiene (explicit allocator parameters, paired `defer`/`errdefer` cleanup, arenas for short-lived allocations), error unions vs optionals, error sets over `anyerror`, `std.testing` patterns, and `zig build` / `zig fmt` tooling. Use for ANY Zig coding task -- adding features, fixing bugs, writing tests, refactoring, build files. Composes on the `programmer` skill; does not duplicate its content.
---

# Zig programmer

Before writing Zig code, read the `programmer` skill and the `git-commit`
skill. This skill covers what is unique to Zig and assumes both are loaded.

## 1. Naming

Follow Zig conventions:
- `PascalCase` for types and for functions that return a type (the "type
  function" idiom: `ArrayList(T)`, `HashMap(K, V)`).
- `camelCase` for functions and methods: `init`, `deinit`, `parseHeader`.
- `snake_case` for variables, fields, parameters, and constants of non-type
  values: `buf_len`, `max_workers`, `attempt`.
- File names are `snake_case.zig`, except a file that exists solely to define
  one type, in which case the file may take the type's `PascalCase` name so
  `@import("Foo.zig")` reads as the type itself.
- Acronyms follow the case of the surrounding identifier:
  `parseHttpHeader` (camelCase context, not `parseHTTPHeader`),
  `HttpServer` (PascalCase context, not `HTTPServer`).
- Do not call module imports `const <foo>_mod = @import(...)`. Use the same
  name for the module constant as the file name, unless there is an actual
  collision. Typically it is better to resolve the collision and rename the
  variable that would cause it than to name the module `*_mod`.

Namespaces in Zig are values, not a separate construct. A `const` bound to a
struct is a namespace; name it for what it contains, not `util`/`common`/
`helper`.

## 2. Error sets over `anyerror`

Prefer a named error set or an inferred one (the `!T` shorthand on a function
signature) over `anyerror`. `anyerror` is the universal set -- it is fine for
function pointers stored in tables (where every callee may fail differently)
and for top-level glue, but using it inside a single module erases the
information `errors.Is`-style branching depends on.

```zig
const ParseError = error{ Empty, TooLong, BadDigit };

pub fn parse(s: []const u8) ParseError!u32 { ... }
```

If you do not have a reason to enumerate, omit the set and let the compiler
infer it: `pub fn parse(s: []const u8) !u32`. The inferred set still gives
callers exhaustive `switch` coverage.

## 3. Error handling

Handle errors immediately with `try`, `catch`, or by surfacing them up the
call chain. Do not bind an error union to a name and check it several lines
later -- that defeats the type system's pressure to handle the failure path.

```zig
const buf = try allocator.alloc(u8, len);
errdefer allocator.free(buf);
```

`try` propagates; `catch |err|` recovers or transforms; `catch unreachable`
asserts the failure cannot happen and crashes loudly if it does. Reach for
`catch unreachable` only when the input has already been validated -- do not
use it to silence errors you have not actually ruled out.

Do not log and return the same error -- either handle it (log) or propagate
it (return). The caller decides whether to log.

When you need to add context to an error, return a *different* error from a
local set rather than wrapping a string into the error itself. Zig errors do
not carry payloads; the context belongs in a log line or in a separate
out-parameter struct that the caller can inspect on the failure path. When
using an out-parameter, favor the name `diagnostic`, or be a struct that has
a `diagnostic` field that the error details are put into.

## 4. Allocators

Memory management is the part of Zig that has no Go analog. Get this right
and the rest of the language follows.

**Pass allocators in, do not pick them.** Any function that allocates takes
an `Allocator` parameter, usually as the first argument. The caller chooses
the allocator (page allocator, GPA, arena, fixed buffer); the callee just
asks for memory. This is what makes Zig code testable with
`std.testing.allocator` and reusable across short-lived and long-lived
contexts. Call the allocator parameter `allocator` unless there is a good
reason to do otherwise. Do not mix allocator parameter names within the
same project.

**Pair every allocation with its cleanup at the point of allocation.** Use
`defer` for unconditional cleanup and `errdefer` for cleanup on the error
path only:

```zig
const buf = try allocator.alloc(u8, n);
errdefer allocator.free(buf);

try fillBuffer(buf);
return buf; // success: ownership transfers to caller, errdefer does not fire
```

The pairing matters because cleanup that lives several lines away from the
allocation rots when the function is edited. Keep them together.

**Arenas for many short-lived allocations.** When a function allocates many
small things that all live for the same scope (parsing, request handling,
one-shot computations), use an `ArenaAllocator` and free it once at the end
rather than tracking each allocation individually:

```zig
var arena = std.heap.ArenaAllocator.init(parent);
defer arena.deinit();
const a = arena.allocator();
// many small allocations from `a`; one free at the defer
```

**Document who owns the result.** A function returning `![]u8` should make
clear in its name or doc comment whether the caller frees it (`alloc...`,
`dupe...`) or it is owned elsewhere (a slice into a buffer, a borrowed
reference). Mismatched ownership is the leading source of leaks and
double-frees in Zig.

**Test with `std.testing.allocator`.** It detects leaks at the end of each
test. If a test passes with the testing allocator, the code under test is
leak-clean for the paths it exercised.

## 5. Io

Since Zig 0.16, Io happens through a parameter, similar to how allocators
work. By default, pass this as the parameter named `io` after `allocator`,
if it exists, or as the first parameter if there is no allocator.

## 6. Optionals vs error unions

`?T` means "this value may be absent for an expected reason." `E!T` means
"this operation may fail." Do not conflate them.

- Use `orelse` to provide a default or to propagate absence:
  `const name = lookup(id) orelse return error.Unknown;`
- Use `if (x) |val| { ... } else { ... }` to branch on presence.
- Returning `?T` and using `null` to mean "failure" hides the failure mode
  from callers. Return `!T` instead, and let the error set name what went
  wrong.

A function that can both fail *and* return nothing-meaningful uses `!?T` --
this is a bit awkward to read but is the honest signature; do not collapse
it into `?T` to look cleaner.

## 7. Tests

Tests live in the same file as the code they exercise, in `test "name"`
blocks. They are picked up by `zig build test`.

```zig
test "parse rejects empty input" {
    try std.testing.expectError(error.Empty, parse(""));
}

test "parse golden path" {
    try std.testing.expectEqual(@as(u32, 42), try parse("42"));
}
```

Use `std.testing.expect`, `expectEqual`, `expectEqualStrings`,
`expectError`, and `expectEqualSlices` rather than rolling your own
assertions. `expectEqual` infers the expected type from the actual value, so
either cast the expected value (`@as(u32, 42)`) or pass the actual first.

Always test with `std.testing.allocator` so leaks are caught:

```zig
test "parse allocates correctly" {
    const a = std.testing.allocator;
    var result = try parseList(a, "a,b,c");
    defer result.deinit(a);
    try std.testing.expectEqual(@as(usize, 3), result.items.len);
}
```

The unimplemented skeleton idiom (see the `programmer` skill): write the
function signature with `@panic("unimplemented")` in the body, then write
the tests against that signature, then fill in the body. The tests fail at
runtime when they hit the panic, which is the red step.

Table-driven tests in Zig:

```zig
test "parse cases" {
    const cases = [_]struct {
        input: []const u8,
        expected: anyerror!u32,
    }{
        .{ .input = "0",  .expected = 0 },
        .{ .input = "42", .expected = 42 },
        .{ .input = "",   .expected = error.Empty },
    };
    for (cases) |c| {
        try std.testing.expectEqual(c.expected, parse(c.input));
    }
}
```

Use `github.com/cloudboss/zest` as the test runner to show PASS/FAIL output,
and to enable setup and teardown, which is especially useful for integration
tests.

```
zig fetch --save git+https://github.com/cloudboss/zest
```

```
const tests = b.addTest(.{
    .root_module = b.createModule(.{
        .root_source_file = b.path("src/root.zig"),
        .target = target,
        .optimize = optimize,
    }),
    .test_runner = .{
        .path = zest.path("src/root.zig"),
        .mode = .simple,
    },
});
```

Setup and teardown is done with special tests named `zest.beforeAll`,
`zest.afterAll`, `zest.beforeEach`, and `zest.afterEach`.

## 8. `comptime` and generics

Use `comptime` for what compile-time constant computation, type-parameterised
data structures via "function returning a type", and fail-fast invariants via
`@compileError`. Do not reach for it as a clever substitute for a runtime
function; the error messages are worse and the code becomes hard to follow.

A type function is the idiomatic generic:

```zig
pub fn RingBuffer(comptime T: type, comptime cap: usize) type {
    return struct { ... };
}
```

If your `comptime` block is more than a handful of lines, consider whether
the work could happen at runtime in `init` instead. Compile-time code that
is hard to step through is hard to maintain.

## 9. Tooling

If the project uses Make, use Make targets for building and testing.
Typically, this will be:
- Integration tests: `make test-integration` or similar
- Unit tests: `make test`
- Build: `make build`
- Release: `make release`

If these Make targets exist, DO NOT use bare `zig build` commands.

Otherwise, run from the project root:
- Build: `zig build`.
- Test: `zig build test`.
- Run: `zig build run -- <args>`.

`build.zig` is itself Zig code -- the same naming, formatting, and clarity
rules apply. When adding a dependency, update `build.zig.zon` and wire the
module into both the library `mod` and the executable's `imports` if both
need it.

Pin the Zig version the project expects (often via `Makefile`, `flake.nix`,
or a `.tool-versions` file). Zig has frequent breaking changes between
minor versions; mismatches produce confusing errors.

## 10. Formatting and line length

Run code through `zig fmt`. It is the canonical formatter and the only one --
do not bikeshed style choices it has already decided.

Break lines that exceed 100 characters. Do not reflow lines that are already
under 100 characters -- only break when the line is too long. When breaking a
long line, start the continuation at a logical point (after a comma, after an
operator, or at a struct/argument boundary) and let `zig fmt` handle the
indentation.

## 11. Committing

Before committing, ensure the following:
- Integration tests pass.
- Unit tests pass.
- The build succeeds for every target the project produces.
- Code follows the formatting and line length rules. If the formatter touches
  files outside the working tree of this commit, do not include them; instead
  make a separate formatting commit for the rest.

To commit, use the `git-commit` skill. Do not run `git commit` directly.
