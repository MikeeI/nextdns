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
Impact [O]: Concurrent pipeline responses produced an undecodable first DNS TCP frame.

## Evidence

- [S] `proxy/tcp.go:83-86,138-166` starts parallel writers and performs two writes per frame.

## Bug-Reproduction

Environment: 2026-10-31; Go 1.27.3; Linux/amd64; source anchors match `Source`.
Command: `go run /tmp/nextdns-semantic-smoke.EhEc1u/main.go /tmp/nextdns-semantic-smoke.EhEc1u/extra.go /tmp/nextdns-semantic-smoke.EhEc1u extra`.
Scenario: Admit 128 pipelined queries, release their response handlers together, and decode the TCP stream.
Result [O]: `tcp_pipeline requests=128 valid_frames=0 framing_error=unpacking Question.Name: segment prefix is reserved`.
The 17-item semantic review retained this defect as `E2`, severity `High`; production frequency was not measured.
The temporary harness was removed; reconstruct a standalone reproducer before publication.

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

A retained standalone reproducer, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Recreate TCP framing reproducer
Action: Reconstruct the synchronized parallel-response scenario over a real loopback TCP connection.
Done-When: Retain a runnable command and the frame decoding failure.
