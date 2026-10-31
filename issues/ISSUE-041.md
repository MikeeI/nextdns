# ISSUE-041 — control: send deadlines omit lock and socket writes

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

Root-Cause [S]: `SendContext` checks cancellation only after uninterruptible mutex acquisition and socket writing.
The five-second context created by `Send` does not bound either preceding phase.

## Reach-and-Impact

Reach [S]: Both control send methods use this path; large writes or lock contention expose the missing bound.
Impact [O]: A write to a non-reading peer outlived its 20 ms deadline and remained blocked after 120 ms.
Impact [S]: Waiting for the send mutex cannot observe its caller's cancellation.
The 17-item semantic review retained this API defect as `E14`, severity `Medium`.

## Evidence

- [S] `ctl/client.go:53-70` creates a timeout but acquires the mutex and writes before selecting on its context.
- [O] Explicitly closing the connection unblocked the smoke's expired send.

## Bug-Reproduction

Environment: 2026-10-31; Go 1.27.3; Linux/amd64; source anchors match `Source`.
Command: `go run /tmp/nextdns-semantic-smoke.EhEc1u/main.go /tmp/nextdns-semantic-smoke.EhEc1u/extra.go /tmp/nextdns-semantic-smoke.EhEc1u extra`.
Scenario: Send a 4 MiB event to an accepted Unix socket whose peer never reads, with a 20 ms context deadline.
Result [O]: `control_write_deadline returned_within_120ms=false context_error=context deadline exceeded`.
Only the blocked-write path was executed; lock-wait cancellation remains source-proven.
The temporary harness was removed; reconstruct a standalone reproducer before publication.

## Prior-Art

Coverage: Local ledger searched; no existing record owns cancellation before the reply-wait phase.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Make send-lock acquisition cancellable and bound the acquired owner's socket write with deadline and cancellation.

## Scope-and-Constraints

- Preserve serialized send ownership and the existing event format.
- A cancelled waiter must not close the connection used by a different active sender.
- Distinguish cancellation coverage from late-reply correlation in `ISSUE-040`.

## Verification

- Use a non-reading peer and verify an owned write returns when its context expires.
- Cancel a lock waiter and verify prompt return without interrupting the active sender.

## Publication-Blockers

A retained standalone reproducer, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Recreate control cancellation reproducer
Action: Reconstruct blocked-write and cancelled-lock-wait scenarios for the control client.
Done-When: Retain runnable commands and observed cancellation latency for both blocking phases.
