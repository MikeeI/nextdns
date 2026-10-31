# ISSUE-040 — control: late replies satisfy later same-name requests

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: Medium
Root-Cause-Confidence: High
Finding-Category: API
Created: 2026-10-31
Updated: 2026-10-31
Source: `upstream/master@63564a9733bcb214d9acfde8ba31357011617f4b`

## Root-Cause

Root-Cause [S]: `SendContext` correlates replies only by event name and retains the connection after timeout.
A late reply can satisfy a later request with the same name.

## Reach-and-Impact

Reach [S]: The exported client supports repeated sends over one connection; the CLI sends once before closing it.
Impact [O]: After the first request timed out, the second same-name request returned the first request's data.
The 17-item semantic review retained this API defect as `E13`, severity `Medium`.

## Evidence

- [S] `ctl/client.go:60-76` returns on context cancellation without invalidating the shared reply stream.
- [S] `ctl.go:19-29` uses one send per CLI connection; ordinary single-send commands are not shown affected.
- [O] The local smoke returned `second_reply=first expected=second` after `context deadline exceeded`.

## Bug-Reproduction

Environment: 2026-10-31; Go 1.27.3; Linux/amd64; source anchors match `Source`.
Command: `go run /tmp/nextdns-semantic-smoke.EhEc1u/main.go /tmp/nextdns-semantic-smoke.EhEc1u`.
Scenario: Delay the first `same` command past a 20 ms context deadline, then send `same` with different data.
Result [O]: `control_timeout first_error=context deadline exceeded second_reply=first expected=second`.
The temporary harness was removed; reconstruct a standalone reproducer before publication.

## Prior-Art

Coverage: Local ledger searched; no existing record owns reply correlation after timeout.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Invalidate the connection after a sent request times out so late replies cannot satisfy later requests.

## Scope-and-Constraints

- Preserve successful serialized sends and the existing event format.
- Distinguish this correlation defect from blocked-write and lock cancellation in `ISSUE-041`.
- Do not introduce request IDs when connection invalidation is sufficient.

## Verification

- Delay one reply beyond timeout, reuse the event name, and reject the old response as a new success.
- Verify the timed-out connection cannot be reused for an ambiguous request.

## Publication-Blockers

A retained standalone reproducer, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Recreate control correlation reproducer
Action: Reconstruct the delayed-reply scenario over one isolated control connection.
Done-When: Retain a runnable command and results proving that the second request receives the first reply.
