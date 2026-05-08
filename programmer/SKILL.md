---
name: programmer
description: Cross-language programming idioms and discipline -- minimal comments that reflect the current state of the code (never dev history, deleted code, or "phases"), language-native naming and conventions, red/green TDD (failing test first for bugs, skeleton-first for new functions), and committing through the git-commit skill. Use this skill for ANY coding task -- adding a feature, fixing a bug, writing tests, refactoring -- even when the user does not explicitly ask for "best practices." Language-specific programmer skills (zig-programmer, python-programmer, rust-programmer, etc.) reference this as the canonical source of shared idioms; do not duplicate this content in those skills.
---

# Programmer

Shared idioms for writing software in any language. Language-specific programmer
skills compose on top of this -- they cover what is unique to the language
(error handling, allocator patterns, ownership, async story, formatter), while
this skill covers what does not change between languages.

## Why these rules exist

Code outlives the conversation that produced it. Every comment, name, test, and
commit either helps a future reader (who may be you in three months) understand
the code, or distracts from it. The rules below are about keeping signal high
and noise low. They are not ceremony.

## 1. Comments

Default to writing no comments. A well-named function, with well-named
parameters and locals, already explains *what* the code does -- a comment that
restates the name is noise. Only write a comment when the **why** is non-obvious:
a hidden constraint, a workaround for a specific bug, a subtle invariant, a
piece of behavior that would surprise a reader.

**Comments must reflect the current state of the code.** A comment that
describes the development process is a bug. So:

- Do not reference deleted or replaced code ("removed the old foo handler",
  "used to call bar()", "this used to be X but now is Y" -- just describe what
  it is now, or say nothing).
- Do not reference work history or "phases" ("phase 2 cleanup", "after the
  refactor", "TODO when we finish migrating").
- Do not address the reviewer or the task ("addresses #123", "for the new auth
  flow", "as discussed").

The test for whether a comment belongs in the code: imagine someone reading the
file two years from now with no knowledge of the change that introduced it.
Would the comment still be true and useful? If not, do not write it. Comments
age into lies; the fewer you write, the fewer lies the codebase contains.

**A comment above a large block of code is usually a smell.** It is the code
asking to be extracted into a named function. The function name *is* the
comment, and unlike a comment it cannot drift out of sync with the code.

Comment format:
Capitalized first word, end with period. Generally avoid inline comments unless it
is *very* trivial. But usually a comment above the line would serve just as well.

## 2. Language idioms and naming

Follow the conventions of the language you are writing in. Each language has a
community style guide, naming conventions (`snake_case` vs `camelCase` vs
`PascalCase`), idiomatic patterns for error handling, iteration, and
concurrency, and a standard-library shape. Match those.

The reason is not aesthetic. Idiomatic code is the code other contributors and
tools (linters, formatters, IDEs, language servers) expect. Non-idiomatic code
creates friction every time someone reads or modifies it, and tooling silently
loses leverage.

When in doubt, mirror the surrounding code in the file or module. The local
style is more authoritative than any global guide -- consistency with the
neighbors is more valuable than being globally "correct."

A language-specific programmer skill (e.g., `zig-programmer`) is the place for
the concrete rules. This skill only asserts the principle.

## 3. Committing

When you commit, use the `git-commit` skill rather than running `git commit`
directly. The git-commit skill enforces commit discipline (one logical change
per commit, plain-prose imperative subject, no co-author trailers unless asked)
that is otherwise easy to forget.

Do not commit until the user has given permission to do so -- either for this
specific change, or as a standing instruction. When in doubt, ask.

## 4. Tests: red/green discipline

Every feature and every bug fix gets a test. The discipline differs slightly
between the two cases.

### Bug fixes start red

Before writing the fix, write a test that exercises the bug and **watch it
fail**. Then write the fix and watch it pass. This proves three things at once:

1. The bug actually existed (the failing test reproduced it).
2. Your fix addresses *that specific bug* (the same test now passes).
3. A future regression of that bug will be caught (the test stays in the
   suite).

Skipping the failing-test step is tempting because the fix often feels obvious,
but it loses all three guarantees -- you only learn that *some* test passes,
not that the test you wrote was actually testing the bug.

### New implementations start with skeletons

When adding a new function, method, or type, the order is:

1. **Skeleton.** Write the signature with a body that fails loudly with an
   "unimplemented" marker -- whatever the language's idiomatic form is
   (`@panic("unimplemented")` in Zig, `raise NotImplementedError` in Python,
   `todo!()` in Rust, `panic("unimplemented")` in Go). The skeleton must
   compile / parse so the rest of the code can build against the new shape.
2. **Tests.** Write tests against the skeleton's signature. They should compile
   and link, then fail at runtime when they hit the unimplemented body. This
   is the red step.
3. **Implementation.** Now write the real body, watching the tests turn green.

The point of skeleton-first is that it forces interface design before
implementation. By the time you start writing the body, the tests have already
described what "done" looks like and the rest of the code already builds
against the new shape. The implementation step becomes the easy one, because
all the hard decisions are already made.

### What "comprehensive" means

Cover:

- The golden path (the input the function is designed to handle).
- The obvious edge cases (empty input, boundary values, error paths, the cases
  the language's type system does not catch for you).
- The specific scenario that motivated the feature, or the specific scenario
  that surfaced the bug.

Do not pad the suite with redundant cases that exercise the same code path
through different inputs. A test the suite would still catch without is dead
weight.

## How language-specific skills should reference this

A language-specific programmer skill (e.g., `zig-programmer`) should:

1. Assume this skill is loaded -- do not restate its content.
2. Cover language-specific concerns: error handling, allocators / memory
   model, the build tool, formatter and linter invocation, the test runner
   syntax, the concrete "unimplemented" idiom for skeletons, naming
   conventions, the standard library's shape.
3. Reference this skill by name when calling out a cross-language rule
   ("see the `programmer` skill for comment hygiene").
