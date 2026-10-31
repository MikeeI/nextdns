# ISSUE-036 — service: startup retry ignores cancellation

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: Medium
Root-Cause-Confidence: High
Finding-Category: Reliability
Created: 2026-10-31
Updated: 2026-10-31
Source: `upstream/master@63564a9733bcb214d9acfde8ba31357011617f4b`

## Root-Cause

Root-Cause [S]: The `ENETUNREACH` retry loop sleeps without selecting on the startup context and doubles backoff without a cap.

## Reach-and-Impact

Reach [S]: Service startup supplies a signal-cancelled context.
Impact [A]: Termination can be delayed while network readiness repeatedly fails.

## Evidence

- [S] `host/service/run.go:23-29`; `run.go:59-73`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Use a cancellable retry timer and bounded backoff.

## Scope-and-Constraints

- Preserve network-readiness retry behavior while startup remains active.
- Exclude unrelated DNS request timeouts.

## Verification

- Simulate repeated unreachable-network errors and cancel during backoff.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce startup cancellation delay
Action: Cancel startup while retrying an unreachable network.
Done-When: Record cancellation and process exit timing.
