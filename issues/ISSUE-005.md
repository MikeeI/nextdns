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
Impact [O]: Forced late registration left `ListenAndServe` pending after cancellation.
Impact [S]: A listener missed by the close pass keeps its serving goroutine and shutdown wait alive.

## Evidence

- [S] `proxy/proxy.go:120-168` registers under `closeAllMu` but iterates without it.

## Bug-Reproduction

Environment: 2026-10-31; Go 1.27.3; Linux/amd64; source anchors match `Source`.
Command: `go run /tmp/nextdns-semantic-smoke.EhEc1u/main.go /tmp/nextdns-semantic-smoke.EhEc1u/extra.go /tmp/nextdns-semantic-smoke.EhEc1u extra`.
Scenario: Gate both listener-start log callbacks, cancel, allow the close pass, then release listener creation.
Result [O]: `listener_cancellation returned=false listeners_created_after_cancellation=true` after 200 ms.
The 17-item semantic review retained this defect as `E3`, severity `High`.
The schedule was forced; no production frequency or race-detector result was measured.
The temporary harness was removed; reconstruct a standalone reproducer before publication.

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

A retained standalone reproducer, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Recreate listener shutdown reproducer
Action: Reconstruct cancellation before delayed listener registration.
Done-When: Retain a runnable command showing the pending shutdown and account for all created listeners.
