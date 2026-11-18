# Clarify before acting

An issue is **unclear** when any of these holds: the requirement admits more than one reasonable interpretation, acceptance criteria are missing, an input, output, or error case is unspecified, or a needed constraint (API contract, data shape, performance target, dependency choice) is unknown.

- When an issue is unclear, the agent MUST stop and ask the developer before writing or changing code for the unclear part.
- The agent MUST NOT fill a gap with its own assumption, a guessed default, or a placeholder implementation.
- Each question MUST be concrete: name the gap, list the interpretations the agent sees, and state what each would change in the code.
- The agent SHOULD batch all open questions into one message rather than asking one at a time.
- The agent MAY proceed with parts of the task that are unambiguous and independent of the open questions, and MUST say which parts it is holding back.
- Facts the agent can verify itself (reading the codebase, `go.mod`, tests, `go doc`) are not unclear; the agent MUST look them up instead of asking.
