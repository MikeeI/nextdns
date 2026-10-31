# ISSUE-008 — resolver: stale cache fallback overwrites aliased query

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: High
Root-Cause-Confidence: High
Finding-Category: API
Created: 2026-10-31
Updated: 2026-10-31
Source: `upstream/master@63564a9733bcb214d9acfde8ba31357011617f4b`

## Root-Cause

Root-Cause [S]: Cache fallback copies into the response buffer before the potentially aliased request is sent upstream.

## Reach-and-Impact

Reach [S]: The public resolver contract explicitly allows request and response buffer reuse.
Impact [O]: A DNS53 retry with aliased buffers transmitted an old response with `QR=1` instead of a query.

## Evidence

- [S] `resolver/resolver.go:18-20` permits aliasing.
- [S] `resolver/dns53.go:41-47,66` copies cached data before writing `q.Payload`.
- [S] `resolver/doh.go:76-88` has the corresponding DoH ordering.

## Bug-Reproduction

Environment: 2026-10-31; Go 1.27.3; Linux/amd64; source anchors match `Source`.
Command: `go run /tmp/nextdns-alias-smoke-20261031.go`.
Scenario: Prime a TTL-zero response, then resolve with `q.Payload` sharing the response buffer.
Result [O]: `aliased_stale_cache transmitted_response_bit=true`.
The 17-item semantic review retained this public API defect as `E10`, severity `Medium`.
DNS53 was executed; the corresponding DoH ordering remains source-proven.
The temporary harness was removed; reconstruct a standalone reproducer before publication.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Preserve the outgoing request before the fallback copy or defer copying the fallback until after network I/O.

## Scope-and-Constraints

- Preserve the documented buffer aliasing API.
- Exclude unrelated cache semantics.

## Verification

- Use a shared request and response buffer with a stale entry and inspect upstream bytes.

## Publication-Blockers

A retained standalone reproducer, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Recreate alias buffer reproducer
Action: Reconstruct the stale-cache retry with shared request and response storage.
Done-When: Retain a runnable command and the upstream-observed QR bit.
