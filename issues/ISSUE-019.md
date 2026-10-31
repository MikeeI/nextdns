# ISSUE-019 — discovery: failed semaphore acquisition releases another owner

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

Root-Cause [S]: `runSingle` defers release even when acquisition failed.

## Reach-and-Impact

Reach [S]: The semaphore is intended to prevent concurrent recursive lookups for one key.
Impact [O]: After a rejected second lookup, a third lookup entered while the original lookup was still running.

## Evidence

- [S] `discovery/dns.go:117-126`; `discovery/semaphore.go:10-26`.

## Bug-Reproduction

Environment: 2026-10-31; Go 1.27.3; Linux/amd64; source anchors match `Source`.
Command: `go run /tmp/nextdns-semantic-smoke.EhEc1u/main.go /tmp/nextdns-semantic-smoke.EhEc1u/extra.go /tmp/nextdns-semantic-smoke.EhEc1u extra`.
Scenario: Hold the first lookup's A/AAAA replies, reject a second lookup, then attempt a third for the same key.
Result [O]: `discovery_loop_guard second_result=[] concurrent_queries_before_release=4 expected=2`.
The 17-item semantic review retained this defect as `E12`, severity `Medium`.
No production recursive loop or amplification rate was measured.
The temporary harness was removed; reconstruct a standalone reproducer before publication.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Release the key only after successful acquisition.

## Scope-and-Constraints

- Preserve DNS loop prevention.
- Exclude unrelated lookup caching.

## Verification

- Block one lookup and execute two further lookups of the same key.

## Publication-Blockers

A retained standalone reproducer, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Recreate discovery ownership reproducer
Action: Reconstruct the blocked-first, rejected-second, admitted-third lookup sequence.
Done-When: Retain a runnable command and upstream counts before releasing the original owner.
