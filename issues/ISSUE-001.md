# ISSUE-001 — config: destructive saves ignore write failures

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: High
Root-Cause-Confidence: High
Finding-Category: Reliability
Created: 2026-10-31
Updated: 2026-10-31
Source: `upstream/master@63564a9733bcb214d9acfde8ba31357011617f4b`

## Root-Cause

Root-Cause [S]: `SaveConfig` truncates the existing file and ignores write and close errors.

## Reach-and-Impact

Reach [S]: Configuration saves use `host/service/config.go:225-296`.
Impact [A]: Failed writes or interruption can leave incomplete configuration; runtime reproduction is pending.

## Evidence

- [S] `host/service/config.go:259-296` opens with `O_TRUNC`, ignores output errors, and returns nil.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Write a temporary file, check write and close errors, and atomically replace the original.

## Scope-and-Constraints

- Preserve existing configuration and comments on failure.
- Exclude unrelated configuration changes.

## Verification

- Inject write and close failures; verify the original file remains intact.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce failed config save
Action: Reproduce a failed save without affecting real configuration.
Done-When: Record the command, result, and preserved or lost file contents.
