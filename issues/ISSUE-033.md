# ISSUE-033 — forwarder: configured maximum TTL is not propagated

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: Medium
Root-Cause-Confidence: High
Finding-Category: Correctness
Created: 2026-10-31
Updated: 2026-10-31
Source: `upstream/master@63564a9733bcb214d9acfde8ba31357011617f4b`

## Root-Cause

Root-Cause [S]: Runner configuration sets `MaxTTL` on the main resolver but not configured forwarders.

## Reach-and-Impact

Reach [S]: The CLI promises a maximum TTL handed out to clients.
Impact [A]: Forwarded responses can retain TTLs above the configured maximum.

## Evidence

- [S] `config/config.go:158-165`; `run.go:348-350,439-453`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Set DNS53 and DoH `MaxTTL` on each configured forwarder.

## Scope-and-Constraints

- Preserve upstream TTLs stored in cache for freshness.
- Exclude unrelated timeout settings.

## Verification

- Query controlled forwarders returning TTLs above the configured limit.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce forwarded TTL mismatch
Action: Query DNS53 and DoH forwarders with a low maximum TTL.
Done-When: Record returned TTLs on misses and hits.
