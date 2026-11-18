# Testing

## Test file placement

Tests for the code in a Go source file live next to it, in a file with the same base name plus `_test.go`.

- Tests for code in `<name>.go` MUST be written in `<name>_test.go` in the same directory, for example tests for `order/service.go` go in `order/service_test.go`.
- When `<name>_test.go` already exists, the agent MUST add the new tests to it instead of creating another test file.
- The agent MUST NOT put tests in a separate `test/` or `tests/` directory, or in a test file whose name does not match a source file, such as `service_extra_test.go`.
- Test helpers shared by several test files in one package MUST live in that package's `helpers_test.go`, which MUST NOT contain `Test`, `Benchmark`, `Fuzz`, or `Example` functions.
- Test helpers shared across packages MUST live in an `internal/testutil` package.
- When a test exercises code spread across several files, the agent MUST place it in the `_test.go` file of the source file that defines the function or method under test.
- The agent MAY use either the same package or the external `<package>_test` package inside that file.

## Assertions

Tests check results with `github.com/stretchr/testify`.

- The agent MUST use `assert` for checks on the result under test, so one run reports every failing check.
- The agent MUST use `require` for preconditions the rest of the test depends on, such as `require.NoError` before using a returned value, so the test stops instead of panicking or reporting follow-on failures.
- The agent MUST NOT write hand-rolled checks such as `if got != want { t.Errorf(...) }` where an `assert` or `require` function expresses the same check.
- When `go.mod` does not list `github.com/stretchr/testify`, the agent MUST add it with `go get github.com/stretchr/testify` before writing the tests.

## Table-driven tests

A test is **table-driven** when its cases are rows in a slice or map of structs, and one loop runs each row as a subtest through `t.Run`.

- When two or more cases exercise the same function with different inputs and expected outputs, the agent MUST write them as a table-driven test.
- Each row MUST carry a descriptive `name` field that is passed to `t.Run`, so a failure identifies the case without reading the table.
- When adding a case to a function that already has a table-driven test, the agent MUST add a row to the existing table instead of writing a separate test function.
- The agent MUST NOT duplicate near-identical test functions or copy-paste assertion blocks for cases that fit one table.
- The agent MAY write a standalone test when a table does not fit: a single case, or cases whose setup, steps, or assertions differ so much that a table would need per-row functions or branching on the row to run them.
