# Agentic Skills Security Rules

Semgrep-compatible security rules for scanning **AI agent skill files** (`SKILL.md`,
`skills/` directories) against the [OWASP Agentic Skills Top 10 (AST10)](https://owasp.org/www-project-agentic-skills-top-10/).

Agent skills are Markdown/YAML instruction files that direct AI coding agents
(GitHub Copilot, Claude Code, Codex, OpenClaw, and others). They are executable
in effect but rarely reviewed like code. These rules detect the highest-signal
static indicators of malicious, over-privileged, or tamperable skills.

## Quick start

Works with [Semgrep CE](https://github.com/semgrep/semgrep) or
[Opengrep](https://github.com/opengrep/opengrep):

```bash
# Scan a repository's skill files
semgrep scan --config rules/ /path/to/repo

# or with Opengrep
opengrep scan --config rules/ /path/to/repo
```

### Scanning large trees

Per-rule `paths.include` filters findings but does **not** prune the file walk —
Semgrep still enumerates and reads every file under the target, which is slow
on large monorepos or a directory of many clones. Restrict the walk up front:

```bash
# Only walk files the rules can match
semgrep scan --config rules/ \
  --include '**/SKILL.md' --include '**/skills/**' --include '**/*.skill.md' \
  /path/to/repos

# or pre-filter with find and pass explicit targets
find /path/to/repos \( -name 'SKILL.md' -o -name '*.skill.md' -o -path '*/skills/*' \) \
  -type f | xargs semgrep scan --config rules/
```

> [!TIP]
> When using `--json` output, Semgrep CE may replace the matched-source
> `lines` field with `"requires login"`. Rely on `path`, `start`/`end`, and
> `check_id` when building reports, or re-read snippets from the files.

### GitHub Actions

```yaml
name: Skill security scan
on: [pull_request]

permissions: {}

jobs:
  scan:
    runs-on: ubuntu-latest
    timeout-minutes: 15
    permissions:
      contents: read
      security-events: write
    steps:
      - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1
        with:
          persist-credentials: false
      - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1
        with:
          repository: computersarebad/agentic-skills-rules
          ref: main # pin to a tag or commit SHA for reproducible scans
          path: .agentic-skills-rules
          persist-credentials: false
      - name: Run Semgrep
        run: |
          pip install semgrep==1.174.0
          semgrep scan --config .agentic-skills-rules/rules/ \
            --sarif --output results.sarif --exclude .agentic-skills-rules .
      - name: Upload SARIF to code scanning
        uses: github/codeql-action/upload-sarif@4c0873ef8656cb3c50b3f42fb63bc1ade0cfa827 # v4
        with:
          sarif_file: results.sarif
```

## Rules

### Tier 1 — high confidence

| Rule | Detects | AST10 risk | Checklist | CWE |
| --- | --- | --- | --- | --- |
| `skill-curl-pipe-shell` | Remote script piped directly into a shell | [AST01] | 1.4 | CWE-494 |
| `skill-reverse-shell` | `/dev/tcp`, `nc -e`, `mkfifo`, `socat` reverse-shell primitives | [AST01] | 1.4 | CWE-506 |
| `skill-exfil-endpoint` | Disposable webhooks, paste services, tunnel domains | [AST01] | 1.4 | CWE-201 |
| `skill-ip-literal-url` | Raw IP address URLs (C2 pattern) | [AST01] | 1.4 | CWE-829 |
| `skill-base64-decode-exec` | Base64-decoded payload executed in a shell | [AST01]/[AST04] | 1.4, 4.2 | CWE-506 |
| `skill-shell-profile-persistence` | Writes to `~/.bashrc`, crontab, LaunchAgents, systemd | [AST01] | 1.6 | CWE-506 |
| `skill-agent-identity-write` | Writes to agent identity/memory files (persistence) | [AST01]/[AST03] | 1.6, 3.6 | CWE-829 |
| `skill-credential-file-access` | SSH keys, cloud credentials, tokens, wallets | [AST03] | 3.8 | CWE-522 |
| `skill-env-harvest` | Environment dumps, echoing secret variables | [AST03] | 3.8 | CWE-526 |
| `skill-keychain-access` | macOS Keychain, libsecret, Windows Credential Manager | [AST03] | 3.8 | CWE-522 |
| `skill-browser-data-paths` | Browser saved logins, cookies, profile databases | [AST03] | 3.8 | CWE-522 |
| `skill-hidden-unicode` | Zero-width / bidi Unicode (ASCII smuggling) | [AST04] | 4.2 | CWE-1007 |
| `skill-html-comment-instructions` | Instructions hidden in HTML comments | [AST04] | 4.2 | CWE-1007 |
| `skill-insecure-http-fetch` | External fetches over cleartext HTTP | [AST05] | 5.2, 5.4 | CWE-319 |

### Tier 2 — medium confidence (review findings, tune for your environment)

| Rule | Detects | AST10 risk | Checklist | CWE |
| --- | --- | --- | --- | --- |
| `skill-prompt-injection-phrases` | Instruction overrides, user-concealment phrasing | [AST04]/[AST08] | 4.1, 8.2 | CWE-1427 |
| `skill-unpinned-instruction-fetch` | Mutable external instruction references (branch URLs) | [AST05] | 5.2, 5.4 | CWE-829 |
| `skill-url-shortener` | Shortened links hiding true destination | [AST05] | 5.4 | CWE-829 |
| `skill-unpinned-dependency-install` | `pip`/`npm`/`gem` installs without exact version pins | [AST02] | 2.3 | CWE-1357 |
| `skill-sudo-or-world-writable` | `sudo` usage, `chmod 777` | [AST03] | 3.2 | CWE-732 |

[AST01]: https://owasp.org/www-project-agentic-skills-top-10/ast01.html
[AST02]: https://owasp.org/www-project-agentic-skills-top-10/ast02.html
[AST03]: https://owasp.org/www-project-agentic-skills-top-10/ast03.html
[AST04]: https://owasp.org/www-project-agentic-skills-top-10/ast04.html
[AST05]: https://owasp.org/www-project-agentic-skills-top-10/ast05.html
[AST08]: https://owasp.org/www-project-agentic-skills-top-10/ast08.html

### Scope

Rules apply to files matching `**/SKILL.md`, `**/skills/**`, and `**/*.skill.md`.
`skill-hidden-unicode` is further restricted to Markdown files, because skill
bundles commonly vendor assets (XML schemas, fixtures, fonts) where BOMs and
format-control characters are legitimate.

Note that `**/skills/**` matches *every* file under a skills directory —
helper scripts, configs, and vendored assets included. That is intentional
(bundled scripts are part of the skill's attack surface), but expect findings
in non-Markdown files and triage accordingly.

A finding is not proof of malice — it marks a pattern that a human reviewer
should consciously approve (e.g., a legitimate `curl | bash` install
instruction still deserves a checksum-verified alternative). Tier 2 rules in
particular flag common benign patterns — e.g. `skill-sudo-or-world-writable`
matches remediation docs like "run `sudo apt install python3-venv`", and
`skill-unpinned-dependency-install` fires on any tutorial-style install
command — so treat them as review queues, not verdicts.

### What static rules cannot catch

Per [AST08 — Poor Scanning](https://owasp.org/www-project-agentic-skills-top-10/ast08.html),
pattern matching alone is insufficient. Pair these rules with:

- **Semantic/LLM review** of instruction intent vs. declared description (AST01.2, AST08.1)
- **Dynamic behavioral testing** in a sandbox (AST08.5)
- **Skill inventory and governance** (AST09)

## Testing

Each rule ships with an annotated test file (`<rule-id>.test.md`):

```bash
semgrep scan --test rules/
# or
opengrep test rules/
```

Each rule's `paths.include` lists its own test file (e.g.
`**/skill-exfil-endpoint.test.md`) — Semgrep applies path filters during
`--test` runs too, so without that entry the test file would be skipped. This
is why excluding the rules checkout matters when scanning trees that contain it.

## Contributing

Contributions welcome — especially new rules mapped to AST10 checklist items,
platform-specific manifest checks (Claude Code `skill.json`, VS Code
`package.json`), and false-positive reports. Every rule must:

1. Map to at least one AST10 checklist item in `metadata`
2. Include a test file with positive and negative cases
3. Pass `semgrep scan --test rules/`

## License and attribution

Rules are licensed under [MIT](LICENSE). The OWASP Agentic Skills Top 10 is a
project of the [OWASP Foundation](https://owasp.org/www-project-agentic-skills-top-10/),
licensed under [CC-BY-SA-4.0](https://creativecommons.org/licenses/by-sa/4.0/);
rule descriptions reference its risk categories and checklist with attribution
and do not reproduce its text. Not affiliated with or endorsed by OWASP.
