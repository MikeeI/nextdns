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
Impact [A]: An expired cache hit can cause an old response to be transmitted instead of a query.

## Evidence

- [S] `resolver/resolver.go:18-20` permits aliasing.
- [S] `resolver/dns53.go:41-47,66` copies cached data before writing `q.Payload`.
- [S] `resolver/doh.go:76-88` has the corresponding DoH ordering.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Preserve the outgoing request or use a separate fallback buffer.

## Scope-and-Constraints

- Preserve the documented buffer aliasing API.
- Exclude unrelated cache semantics.

## Verification

- Use a shared request and response buffer with a stale entry and inspect upstream bytes.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce aliased buffer corruption
Action: Resolve with aliased buffers and a stale cache entry.
Done-When: Record the actual upstream request bytes.
