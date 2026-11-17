# goagent core rules

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 and RFC 8174 when, and only when, they appear in all capitals.

## Clarify before acting

An issue is **unclear** when any of these holds: the requirement admits more than one reasonable interpretation, acceptance criteria are missing, an input, output, or error case is unspecified, or a needed constraint (API contract, data shape, performance target, dependency choice) is unknown.

- When an issue is unclear, the agent MUST stop and ask the developer before writing or changing code for the unclear part.
- The agent MUST NOT fill a gap with its own assumption, a guessed default, or a placeholder implementation.
- Each question MUST be concrete: name the gap, list the interpretations the agent sees, and state what each would change in the code.
- The agent SHOULD batch all open questions into one message rather than asking one at a time.
- The agent MAY proceed with parts of the task that are unambiguous and independent of the open questions, and MUST say which parts it is holding back.
- Facts the agent can verify itself (reading the codebase, `go.mod`, tests, `go doc`) are not unclear; the agent MUST look them up instead of asking.

## Comment-free generated code

Generated Go code carries its meaning in names, types, and structure.

- Code the agent writes MUST NOT contain comments: no line comments, block comments, doc comments, or TODO/FIXME notes.
- The agent MUST express intent through descriptive identifiers, small functions, and named types or constants instead.
- Compiler and tool directives are code, not comments, and MUST be kept where required: `//go:build`, `//go:generate`, `//go:embed`, `//nolint:<linter>`, and `// Code generated ... DO NOT EDIT.` headers.
- The agent MUST preserve existing comments in code it did not write, unless the developer asks to remove them or the commented code is deleted.
