# ISSUE-006 — cache: query semantics omitted from cache identity

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

Root-Cause [S]: Cache keys omit DNS header flags and EDNS parameters that can affect answers.

## Reach-and-Impact

Reach [S]: DNS53 and DoH use the same key shape when caching is enabled.
Impact [O]: A query with `CD=true` received a cached response with `CD=false` without another upstream call.
Impact [S]: `DO` and other answer-relevant flags are also absent from the key.

## Evidence

- [S] `resolver/cache.go:13-37` keys on context, class, type, and name only.
- [S] `resolver/dns53.go:37-47` and `resolver/doh.go:73-85` consume those keys.
- [S] RFC 4035 §3.2.2 requires the response to reflect the initiating `CD` setting: https://www.rfc-editor.org/rfc/rfc4035#section-3.2.2.

## Bug-Reproduction

Environment: 2026-10-31; Go 1.27.3; Linux/amd64; source anchors match `Source`.
Command: `go run /tmp/nextdns-semantic-smoke.EhEc1u/main.go /tmp/nextdns-semantic-smoke.EhEc1u`.
Scenario: Prime DNS53 caching with `CD=false`, wait for a fresh hit, then send the same question with `CD=true`.
Result [O]: `cache_query_flags requested_cd=true response_cd=false from_cache=true upstream_calls=1`.
The 17-item semantic review retained this defect as `E9`, severity `Medium`.
The observed path was DNS53 with `CD`; DoH and other flag variants remain source-proven.
The temporary harness was removed; reconstruct a standalone reproducer before publication.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Include answer-relevant semantics, especially `CD` and `DO`, in cache identity or bypass unsafe query variants.

## Scope-and-Constraints

- Preserve profile separation and DNS wire semantics.
- Exclude unrelated cache replacement policies.
- Do not infer an observed DNSSEC-validation bypass or claim that NextDNS performs its own signature validation.

## Verification

- Compare cached and uncached queries with differing `CD`, `DO`, `RD`, and relevant EDNS options.

## Publication-Blockers

A retained standalone reproducer, additional flag variants, upstream prior art, direction, and target remain unresolved.

## Next-Action

Summary: Recreate DNS flag collision
Action: Reconstruct the observed CD mismatch and preserve the upstream-call count.
Done-When: Retain runnable requests and results separating the observed CD collision from other unexecuted variants.
