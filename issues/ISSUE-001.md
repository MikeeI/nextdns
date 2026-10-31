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
Impact [O]: A file-size-limited save returned success, lost the old value, and left incomplete output.

## Evidence

- [S] `host/service/config.go:259-296` opens with `O_TRUNC`, ignores output errors, and returns nil.

## Bug-Reproduction

Environment: 2026-10-31; Go 1.27.3; Linux/amd64; source anchors match `Source`.
Command: `go run /tmp/nextdns-semantic-smoke.EhEc1u/main.go /tmp/nextdns-semantic-smoke.EhEc1u`.
Scenario: Save a 4096-byte value over an existing configuration with `RLIMIT_FSIZE=128`.
Result [O]: `config_write_failure returned_error=<nil> stored_bytes=128 old_value_retained=false complete_new_value=false`.
The 17-item semantic review retained this defect as `E1`, severity `High`.
The temporary harness was removed; reconstruct a standalone reproducer before publication.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Write a temporary file, check write, sync, and close errors, and atomically replace the original.

## Scope-and-Constraints

- Preserve existing configuration and comments on failure.
- Exclude unrelated configuration changes.

## Verification

- Inject write and close failures; verify the original file remains intact.

## Publication-Blockers

A retained standalone reproducer, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Recreate config save reproducer
Action: Reconstruct the isolated file-size-limit save scenario without touching real configuration.
Done-When: Retain a runnable command showing success with incomplete output and loss of the previous value.
