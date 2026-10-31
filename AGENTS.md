# Repository Guidelines

<essential-rule>
AGENTS.md is the sole authoritative project context file.
Read and edit AGENTS.md directly.
</essential-rule>

## Project Overview

NextDNS CLI (`github.com/nextdns/nextdns`) is a Go 1.26 DNS-over-HTTPS client for NextDNS and other providers, including split-horizon configurations.
It targets routers and Unix-like systems as well as macOS and Windows.

## Fork & Upstream Contribution Intent

- Official upstream: [nextdns/nextdns](https://github.com/nextdns/nextdns).
- This checkout is the [MikeeI/nextdns](https://github.com/MikeeI/nextdns) fork, not an independently owned product.
- Support upstream with evidence-backed issues, comments, and pull requests; prefer small, high-value corrections.
- On 2026-10-31, the latest 100 closed pull requests included 36 merged and 31 unmerged pull requests opened from forks; this sample is an indicator, not proof of acceptance policy.
- `ISSUES.md` owns the finding overview and ID allocator; `issues/ISSUE-NNN.md` owns each root cause record; `FORMAT.md` owns the workflow.
- Apply `skill-fork-contribution-tracking`, `skill-maintainer-communication`, `skill-semantic-compression-3`, and `skill-git-commit-format` as applicable.
- Search existing work and upstream guidance before proposing a contribution; reproduce bugs against current upstream.
- Recommend a pull request for a bounded verified fix without an active implementation; otherwise consider a comment, issue, or further investigation.
- The user selects `Authorized-Work` and `Publication-Target`; research does not authorize implementation.
- Keep fork-only tracking and personal commits out of upstream contribution diffs.

## Branch Roles

- The canonical contribution base is `upstream/master`.
- The `personal` branch owns fork context and contribution tracking; never submit it upstream.
- Create upstream-ready `feat/issue-NNN`, `fix/issue-NNN`, or `chore/issue-NNN` branches from current `upstream/master`.
- `origin` is the personal fork; `upstream` is `nextdns/nextdns`.

## Finding and Contribution Ledger

- Read `ISSUES.md` at the start of every agent session and consult `FORMAT.md` for the authoritative workflow.
- Search existing records before allocating the permanent `Next finding ID`; create the issue record, index row, and increment together.
- Each finding owns one root cause and starts as `Investigating`, with `Authorized-Work: Not-Selected`, `Publication-Target: Not-Selected`, and `External-Reference: Not published.`
- Update the issue record and index together after changes to projected fields; run the skill's ledger validator after every ledger mutation.
- Distinguish observed behavior, source evidence, and assumptions; do not treat similarity, TODOs, or detector output as established findings.
- `Research-and-Reporting` excludes source implementation; `Pull-Request-Implementation` permits only the recorded authorized scope.
- Pull-request work must be verified, committed, pushed, and `PR-Ready` before publication.

### External publication approval

Show the exact current draft and target before publishing externally, and obtain user approval for that exact draft and target.
Reapproval is required after either changes.
Record the resulting external reference immediately.
Fork commits, pushes, tracking updates, and authorized implementation follow the active repository contract.

## Architecture and Key Directories

- Root Go files own the command, daemon startup, configuration wiring, and service lifecycle.
- `config/` owns configuration and profiles; `proxy/` handles DNS proxying; `resolver/` handles upstream resolution and caching.
- `router/` contains router-specific integrations; `host/`, `discovery/`, `arp/`, and `ndp/` provide host and network discovery.
- `ctl/` provides the control interface; `scripts/` contains development helpers; `.github/` contains CI and issue templates.
- `README.md` links the upstream installation and usage wiki and documents OpenWrt relay behavior.

## Development Commands and Testing

- Run focused package tests with `go test ./<package>`; run broader tests when practical.
- For networking changes cover failure paths, including timeouts, unreachable upstreams, and malformed responses.
- Verify non-target platforms compile when changing platform-specific code.
- Run `go test ./router/openwrt` for OpenWrt relay unit and failure-path tests.
- `router/openwrt/testdata/relay-integration.sh` exercises UCI/rpcd restoration in a disposable OpenWrt container and requires Go and Docker; it does not verify client-visible packets.
- `scripts/binary-size-diff.sh` supports binary-size comparisons; follow upstream guidance when adding dependencies or feature-heavy paths.

## Repository-Specific Constraints

- Preserve privacy: do not add telemetry or sensitive DNS query logging without an explicit requirement.
- Preserve CLI flags, configuration format, defaults, security semantics, startup/shutdown behavior, and service lifecycle unless an intentional change is documented.
- Avoid surprise network calls, hidden retries that block shutdown, and silent security-changing fallbacks.
- Keep cross-platform behavior consistent and isolate necessary platform-specific behavior.
- Router deployments can have very limited storage, memory, and CPU; avoid unbounded caches and costly background work.
- Avoid dependencies where practical; assess transitive dependencies, binary size, memory, disk, and startup costs when adding one.
- Include before/after binary size comparisons in the pull request or commit notes for dependencies or feature-heavy changes.
- Update README and CLI help when changing documented CLI or configuration behavior.
- Upstream commit tags are `feat`, `bug`, `chore`, and `ci`; respect upstream conventions for contribution commits.

## Development Rules

Before launching agents, apply skill-xray, skill-expert, and skill-brutal to the task.
Surface expert-level issues, non-obvious issues, blindspots, stale assumptions, and hidden dependencies.
Also surface missed constraints, edge cases, false positives, verification gaps, overclaims, and weak assumptions.
Identify improvement potential, inefficiencies, and what is wrong without softening.
Use these findings to design safe slices, sequencing, checks, and boundaries for complete agent results.

Every agent prompt must require skill-xray, skill-expert, and skill-brutal for the assigned scope before acting.
It must surface non-obvious issues, blindspots, stale assumptions, hidden dependencies, and edge cases.
It must also surface verification gaps, overclaims, failure modes, weak assumptions, and what is wrong.
The agent must adjust its approach, challenge its assumptions, and flag misleading or incomplete output risks.

Implementation assignments must cover existing patterns, callers, exported-symbol consumers, and failure modes.
They must also cover concurrency safety and lifecycle cleanup.
Each assignment must state `Test decision: none` or `Test decision: update`.
`update` must name the exact existing test that follows an intentional contract change.
Never request new tests.
Prohibit broad edits, unrelated cleanup, and unassigned files.

No vague agents.
Each assignment needs exact targets, non-goals, evidence anchors, acceptance criteria, and an output contract.

Commit completed units continuously.
Before each commit, use skill-git-commit-format to determine whether staged effects are one coherent unit.
The skill owns commit-message format and evidence.
After the boundary is valid, run the repository-owned commit and push workflow.
Do not commit every trivial edit immediately or defer unrelated work into one end-of-session commit.

Every project-level quality command is quiet by default and verbose on demand.
This policy applies regardless of language or toolchain.
It covers Make targets, package scripts, Python CLIs, shell quality gates, and test runners.
Successful checks print only compact status such as `format: ok`, `lint: ok`, `test: ok`, or `check: ok`.
On failure, exit non-zero and print the failing step, exit code, and enough output to act without rerunning.
Full raw output must remain available through `--verbose`, `VERBOSE=1`, or the underlying tool's verbose mode.
New quality commands and future language setup must follow this policy instead of inventing another logging contract.
