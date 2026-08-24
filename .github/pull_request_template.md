<!-- Thanks for contributing! For new or changed rules, please confirm: -->

## What

<!-- What does this PR add or change, and why? -->

## Checklist (for rule changes)

- [ ] Rule maps to at least one AST10 checklist item in `metadata` (with the matching `astNN.html` reference link)
- [ ] Test file (`<rule-id>.test.md`) covers positive (`# ruleid:`) and negative (`# ok:`) cases
- [ ] `semgrep scan --validate --config rules/` passes
- [ ] `semgrep scan --test rules/` passes
- [ ] README rule tables updated (tier, detects, AST10 risk, checklist, CWE)
