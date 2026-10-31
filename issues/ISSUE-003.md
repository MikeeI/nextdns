# ISSUE-003 — proxy: TCP admission closes connections with pending replies

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

Root-Cause [S]: Failed follow-up TCP slot acquisition closes a connection before its accepted responses complete.

## Reach-and-Impact

Reach [S]: TCP and UDP share admission capacity; pre-read reservations also bound accepted idle TCP connections.
Impact [O]: At limit `1`, a valid first TCP query received `EOF` while its upstream response was pending.

## Evidence

- [S] `proxy/tcp.go:50-86` acquires before `readTCP` and dispatches answers asynchronously.
- [S] `proxy/proxy.go:118-146` shares the channel with UDP.
- [S] Earlier imported review items `E3` and `E5` share this root cause; the 17-item review maps it to `E4`.

## Bug-Reproduction

Environment: 2026-10-31; Go 1.27.3; Linux/amd64; source anchors match `Source`.
Command: `go run /tmp/nextdns-semantic-smoke.EhEc1u/main.go /tmp/nextdns-semantic-smoke.EhEc1u`.
Scenario: Send one TCP query with limit `1` and block its resolver before the response is ready.
Result [O]: `tcp_lifetime limit=1 half_close=false response_size=0 read_error=EOF`.
The temporary harness was removed; reconstruct a standalone reproducer before publication.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Keep pre-read reservations and immediate rejection of full new connections.
Before rejecting a follow-up read, finish that connection's accepted responses rather than closing them.

## Scope-and-Constraints

- Preserve bounded resource use and TCP pipelining.
- Exclude upstream admission policy.
- Moving admission after reading alone loses the existing new-connection bound and is not an accepted correction.
- The semantic review withdrew `E11` (half-close); no DNS-level `CloseWrite` guarantee was established.
- RFC 7766 §6.2.4 does not establish that half-close guarantee: https://www.rfc-editor.org/rfc/rfc7766#section-6.2.4.

## Verification

- At limit `1`, deliver the first response before closing its connection.
- Preserve `TestServeTCPConn_FullLimitDoesNotBlock` and `TestServeTCPConn_IdleReadTimeout`.
- Exercise idle TCP connections, saturated slots, delayed replies, and UDP requests without losing resource bounds.

## Publication-Blockers

A retained standalone reproducer, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Recreate TCP admission reproducer
Action: Reconstruct the delayed-first-response failure while retaining immediate rejection of full new connections.
Done-When: Retain the limit-1 result and identify the existing overload checks the correction must preserve.
