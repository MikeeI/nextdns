# ISSUE-010 — proxy: UDP truncation cuts DNS messages at arbitrary bytes

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: High
Root-Cause-Confidence: High
Finding-Category: Correctness
Created: 2026-10-31
Updated: 2026-10-31
Source: `upstream/master@63564a9733bcb214d9acfde8ba31357011617f4b`

## Root-Cause

Root-Cause [S]: UDP response truncation reduces byte length without rebuilding records or header counts.

## Reach-and-Impact

Reach [S]: Oversized DNS responses can be cut through a resource record.
Impact [A]: Some clients may reject the malformed response instead of retrying.

## Evidence

- [S] `proxy/udp.go:140-150` changes length and sets TC without updating DNS structure.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Construct a valid truncated DNS response with complete records and correct counts.

## Scope-and-Constraints

- Preserve client-advertised size limits and TC semantics.
- Exclude upstream transport selection.

## Verification

- Parse boundary-sized truncated wire messages and exercise client TCP fallback.

## Publication-Blockers

Wire reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce malformed truncated replies
Action: Send an oversized resource record through the UDP proxy.
Done-When: Record the wire response and parser result.
