# goagent

A [Claude Code](https://claude.com/claude-code) plugin that makes coding agents behave like disciplined Go engineers: they ask before they assume, and they write self-explanatory code without comments.

## Why

Coding agents fail in two predictable ways on Go projects:

- **Silent assumptions.** Given a vague issue, an agent picks an interpretation and ships it. You find out in review, or in production.
- **Comment noise.** Generated code is padded with comments that restate the code and go stale on the next edit.

goagent loads a short, strict rule set into every session so the agent asks first and writes clean code.

## Features

- **Clarify before acting.** When an issue is unclear, the agent stops and asks concrete questions, listing the interpretations it sees and what each would change in the code. It looks up facts it can verify itself (`go.mod`, tests, `go doc`) instead of asking you.
- **Comment-free generated code.** Intent goes into names, types, and small functions. Directives such as `//go:build`, `//go:generate`, `//go:embed`, `//nolint`, and generated-code headers are kept. Existing comments are preserved.
- **Test file placement.** Tests for `<name>.go` go in `<name>_test.go` in the same directory, never in a separate `tests/` folder or an unrelated file. Shared helpers go in `helpers_test.go` or `internal/testutil`.
- **testify assertions.** Tests use `assert` for results and `require` for preconditions instead of hand-rolled `if ... t.Errorf` checks. testify is added to `go.mod` when missing.
- **Table-driven tests.** Cases that exercise the same function go into one table, each row run as a named subtest via `t.Run`. Standalone tests are kept for cases a table cannot express cleanly.
- **Always on.** A `SessionStart` hook injects the rules into every session, so they apply without the agent having to invoke a skill.
- **RFC 2119 wording.** Every rule uses MUST, SHOULD, and MAY as defined in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119) and [RFC 8174](https://www.rfc-editor.org/rfc/rfc8174), so each requirement is either mandatory or optional.

## Installation

Claude Code 2.1.275 or later:

```
/plugin install goagent --marketplace dangquyitt/goagent
```

Older versions:

```
/plugin marketplace add dangquyitt/goagent
/plugin install goagent@goagent
```

Restart Claude Code after installing so the `SessionStart` hook runs.

### Try it without installing

```sh
git clone https://github.com/dangquyitt/goagent.git
claude --plugin-dir ./goagent
```

## Example

Prompt: `Add a rate limiter to the API.`

Without goagent, the agent picks an algorithm, a key, and limits, and writes the middleware. With goagent, it writes no code yet and asks instead:

> 1. Which API? There are no handlers in this module. Is it in another path, or should I build a `net/http` server?
> 2. Who is limited: the whole server, each client IP, or each API key?
> 3. What are the limits, for example 10 req/s with bursts of 20?
> 4. Token bucket via `golang.org/x/time/rate`, or a standard-library-only window?
> 5. One instance in memory, or several replicas sharing Redis?
> ...

Given a clear spec, it writes code with no comments:

```go
package demo

type Stack[T any] struct {
	items []T
}

func (s *Stack[T]) Pop() (T, bool) {
	var zero T
	if len(s.items) == 0 {
		return zero, false
	}
	lastIndex := len(s.items) - 1
	top := s.items[lastIndex]
	s.items[lastIndex] = zero
	s.items = s.items[:lastIndex]
	return top, true
}
```

## Rules

Each rule is one file under [`rules/`](rules/).

| Rule | Requirement |
| --- | --- |
| Clarify before acting | MUST ask the developer when an issue is unclear. MUST NOT fill gaps with assumptions or placeholder code. |
| Comment-free generated code | Generated code MUST NOT contain comments. Directives MUST be kept. Existing comments MUST be preserved. |
| Test file placement | Tests for `<name>.go` MUST be in `<name>_test.go` in the same directory. MUST NOT use a separate test directory or unrelated file names. |
| Assertions | MUST use testify `assert` for results and `require` for preconditions. MUST NOT hand-roll checks. |
| Table-driven tests | Same-function cases MUST be one table run via `t.Run` with named rows. New cases MUST extend the existing table. |

### Linter compatibility

Comment-free code conflicts with linters that require doc comments on exported identifiers. If you use them, disable those checks:

```yaml
# .golangci.yml
linters-settings:
  revive:
    rules:
      - name: exported
        disabled: true
```

## Project structure

```
.
├── .claude-plugin/
│   ├── plugin.json        # plugin manifest
│   └── marketplace.json   # lets this repo act as its own marketplace
├── hooks/
│   └── hooks.json         # SessionStart hook that loads the rules
├── rules/                 # one file per rule
│   ├── rfc-2119-keywords.md   # shared keyword definitions, loaded first
│   ├── clarify-before-acting.md
│   ├── comment-free-code.md
│   └── testing.md
└── AGENTS.md              # conventions for contributors
```

## Contributing

Issues and pull requests are welcome.

1. Fork the repo and create a branch.
2. Edit a rule or add a new file under `rules/`, one rule per file. Follow the conventions in [`AGENTS.md`](AGENTS.md): every directive uses uppercase RFC 2119 keywords, and each MUST NOT is paired with the positive MUST it protects.
3. Validate the plugin:
   ```sh
   claude plugin validate .
   ```
4. Test the behaviour in a real session:
   ```sh
   claude --plugin-dir .
   ```
5. Open a pull request describing the behaviour change and how you tested it.
