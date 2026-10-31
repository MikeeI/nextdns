# ISSUE-017 — control: startup unlinks an active Unix socket

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

Root-Cause [S]: Unix control listener startup unconditionally removes its socket path.

## Reach-and-Impact

Reach [S]: A second instance can use the same path as a running daemon.
Impact [A]: The running daemon can lose control-socket reachability.

## Evidence

- [S] `ctl/ctl_unix.go:10-16`; `run.go:170-185`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Establish socket ownership atomically and remove only confirmed stale paths.

## Scope-and-Constraints

- Preserve legitimate stale socket recovery.
- Exclude unrelated Windows transport behavior.

## Verification

- Start concurrent instances with one socket path and check the original instance.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce control socket takeover
Action: Start isolated instances using the same Unix socket.
Done-When: Record each listener and control request outcome.
