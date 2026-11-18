# goagent

Claude Code plugin that ships Go coding rules for agents. The `SessionStart` hook in `hooks/hooks.json` injects `rules/rfc-2119-keywords.md` first, then every other `rules/*.md` file, into each session.

## Rule files

Rule files follow the Claude Code `.claude/rules/` convention: one topic per file, each file self-contained.

- Each rule MUST live in its own file under `rules/`, named after its topic in kebab-case, such as `testing.md`.
- A rule file MUST open with a `# ` heading naming the rule.
- `rules/rfc-2119-keywords.md` defines the RFC 2119 / RFC 8174 keywords once for every instruction in the session, not only goagent rules; other rule files MUST rely on it and MUST NOT repeat the interpretation sentence.
- Apart from the keyword definitions, a rule file MUST be understandable on its own; it MUST NOT depend on another rule file being loaded before it.

## Writing agent guidance

- Every directive in a document an agent consumes (rules, skills, agents, commands) MUST use RFC 2119 / RFC 8174 keywords in all capitals: MUST, MUST NOT, SHOULD, SHOULD NOT, MAY, REQUIRED, RECOMMENDED, OPTIONAL.
- Each skill, agent, and command MUST open with the RFC 2119 / RFC 8174 interpretation sentence copied from `rules/rfc-2119-keywords.md`, since it can be loaded without the rules.
- Lowercase "must", "should", "may" MUST NOT appear with normative meaning; rephrase or capitalize.
- Each rule SHOULD state the target behaviour first; a MUST NOT SHOULD be paired with the positive MUST it protects.

## Versioning

Claude Code detects updates only through `version` in `.claude-plugin/plugin.json`; installed copies stay on the old rules until it changes.

- Every PR that changes what an agent receives (a rule file, `hooks/hooks.json`, or the plugin manifest's components) MUST bump `version` following semantic versioning.
- A wording fix that keeps every rule's meaning MUST bump the patch version.
- A new rule, or a new requirement that does not contradict an existing one, MUST bump the minor version.
- Removing or reversing a rule, weakening a MUST to a SHOULD or MAY, or changing how rules load MUST bump the major version once the plugin reaches 1.0.0; before 1.0.0, it MUST bump the minor version.
- PRs that only touch documentation for contributors, such as `README.md` or `AGENTS.md`, MUST NOT bump `version`.
- After merging a version bump, the maintainer MUST create a `v<version>` git tag and a GitHub release that lists the behaviour changes.
