# ISSUE-005 — proxy: listener registration races shutdown

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

Root-Cause [S]: Shutdown reads the listener close list without the mutex used for concurrent registration.

## Reach-and-Impact

Reach [S]: Concurrent listener setup and context cancellation access `closeAll`.
Impact [A]: A late listener can remain open while shutdown waits for its goroutine.

## Evidence

- [S] `proxy/proxy.go:120-168` registers under `closeAllMu` but iterates without it.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Coordinate listener registration and shutdown atomically and close late listeners.

## Scope-and-Constraints

- Preserve cancellation and original listener errors.
- Exclude unrelated request handling.

## Verification

- Synchronize a late bind with cancellation and check shutdown and race detection.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce listener shutdown race
Action: Force listener registration during cancellation.
Done-When: Record race evidence and whether shutdown completes.
