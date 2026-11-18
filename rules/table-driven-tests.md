# Table-driven tests

A test is **table-driven** when its cases are rows in a slice or map of structs, and one loop runs each row as a subtest through `t.Run`.

- When two or more cases exercise the same function with different inputs and expected outputs, the agent MUST write them as a table-driven test.
- Each row MUST carry a descriptive `name` field that is passed to `t.Run`, so a failure identifies the case without reading the table.
- When adding a case to a function that already has a table-driven test, the agent MUST add a row to the existing table instead of writing a separate test function.
- The agent MUST NOT duplicate near-identical test functions or copy-paste assertion blocks for cases that fit one table.
- The agent MAY write a standalone test when a table does not fit: a single case, or cases whose setup, steps, or assertions differ so much that a table would need per-row functions or branching on the row to run them.
