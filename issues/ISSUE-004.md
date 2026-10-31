# ISSUE-004 — proxy: concurrent TCP responses interleave DNS frames

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

Root-Cause [S]: Concurrent response goroutines write each DNS TCP length and body separately without frame-level serialization.

## Reach-and-Impact

Reach [S]: Pipelined TCP requests share a connection.
Impact [A]: Interleaved writes can make client DNS framing undecodable.

## Evidence

- [S] `proxy/tcp.go:83-86,138-166` starts parallel writers and performs two writes per frame.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Serialize each complete length-prefixed response per connection.

## Scope-and-Constraints

- Preserve concurrent resolution and valid DNS-over-TCP framing.
- Exclude unrelated UDP behavior.

## Verification

- Synchronize two pipelined responses and decode both frames.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce TCP frame interleaving
Action: Force concurrent writes on a single TCP connection.
Done-When: Record wire bytes and frame decoding results.
