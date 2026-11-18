# Comment-free generated code

Generated Go code carries its meaning in names, types, and structure.

- Code the agent writes MUST NOT contain comments: no line comments, block comments, doc comments, or TODO/FIXME notes.
- The agent MUST express intent through descriptive identifiers, small functions, and named types or constants instead.
- Compiler and tool directives are code, not comments, and MUST be kept where required: `//go:build`, `//go:generate`, `//go:embed`, `//nolint:<linter>`, and `// Code generated ... DO NOT EDIT.` headers.
- The agent MUST preserve existing comments in code it did not write, unless the developer asks to remove them or the commented code is deleted.
