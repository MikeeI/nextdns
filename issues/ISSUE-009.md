# ISSUE-009 — resolver: DNS53 forwarding cannot complete truncated replies

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

Root-Cause [S]: DNS53 uses UDP only, so a truncated upstream reply cannot be completed through its resolution path.

## Reach-and-Impact

Reach [S]: DNS53 forwarders are advertised as supporting UDP and TCP.
Impact [O]: A reachable TCP-capable upstream returned an incomplete resolver result with no TCP attempt.
Impact [S]: Client TCP requests use the same DNS53 path.

## Evidence

- [S] `config/config.go:139-143`; `resolver/dns53.go:58-81`; `proxy/tcp.go:138-141`.
- [S] RFC 7766 §5 requires forwarding TCP interoperability: https://www.rfc-editor.org/rfc/rfc7766#section-5.
- [S] RFC 5625 §4.4.1 distinguishes TCP forwarding from retry policy: https://www.rfc-editor.org/rfc/rfc5625#section-4.4.1.

## Bug-Reproduction

Environment: 2026-10-31; Go 1.27.3; Linux/amd64; source anchors match `Source`.
Command: `go run /tmp/nextdns-semantic-smoke.EhEc1u/main.go /tmp/nextdns-semantic-smoke.EhEc1u`.
Scenario: Resolve against loopback UDP returning `TC=1` and TCP on the same port offering a complete answer.
Result [O]: `dns53_truncation returned_tc=true answers=0 tcp_attempts=0`.
The 17-item review retained `E6` as `High` but corrected its contract from mandatory retry to TCP interoperability.
The observed boundary was the resolver API, not an end-to-end client TCP exchange.
The temporary harness was removed; reconstruct a standalone reproducer before publication.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

As a small correction, retry `TC=1` over TCP within the existing total deadline and upstream admission.
Do not admit incomplete replies as ordinary fresh cache entries.

## Scope-and-Constraints

- Preserve existing endpoint selection and DNS framing.
- Exclude unrelated DoH behavior.
- TCP interoperability is required; automatic retry is the selected correction, not the only valid transport policy.

## Verification

- Compare controlled UDP truncation with the upstream TCP answer.

## Publication-Blockers

A retained standalone reproducer, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Recreate DNS53 TCP reproducer
Action: Reconstruct the UDP-truncation and complete-TCP upstream scenario.
Done-When: Retain a runnable command, resolver output, and UDP/TCP attempt counts before end-to-end verification.
