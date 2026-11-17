# goagent

Claude Code plugin that ships Go coding rules for agents. `rules/core.md` is injected into every session by the `SessionStart` hook in `hooks/hooks.json`.

## Writing agent guidance

- Every directive in a document an agent consumes (rules, skills, agents, commands) MUST use RFC 2119 / RFC 8174 keywords in all capitals: MUST, MUST NOT, SHOULD, SHOULD NOT, MAY, REQUIRED, RECOMMENDED, OPTIONAL.
- Each such document MUST open with the RFC 2119 / RFC 8174 interpretation sentence copied from `rules/core.md`.
- Lowercase "must", "should", "may" MUST NOT appear with normative meaning; rephrase or capitalize.
- Each rule SHOULD state the target behaviour first; a MUST NOT SHOULD be paired with the positive MUST it protects.
