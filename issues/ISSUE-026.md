# ISSUE-026 — forwarder: DNS domain matching is case sensitive

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

Root-Cause [S]: Forwarder domain equality and suffix checks preserve case.

## Reach-and-Impact

Reach [S]: Query parsing preserves question-name spelling.
Impact [O]: Equivalent lower-case and mixed-case DNS names produced different `Forwarders.Get` matches.

## Evidence

- [S] `config/forwarder.go:33-39,50-58`; `resolver/query/query.go:156-165`.

## Bug-Reproduction

Environment: 2026-10-31; Go 1.27.3; Linux/amd64; source anchors match `Source`.
Command: `go run /tmp/nextdns-semantic-smoke.EhEc1u/main.go /tmp/nextdns-semantic-smoke.EhEc1u`.
Scenario: Configure `corp.example=127.0.0.1:53` and compare `host.corp.example.` with `host.CORP.example.`.
Result [O]: `forwarder_case lower_match=true mixed_match=false`.
The 17-item semantic review retained this defect as `E8`, severity `Medium`.
Resolver selection was observed directly; an end-to-end DNS packet route was not executed.
The temporary harness was removed; reconstruct a standalone reproducer before publication.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Normalize comparison names using ASCII DNS case rules while preserving label boundaries and wire spelling.

## Scope-and-Constraints

- Preserve forwarder precedence and domain-boundary semantics.
- Exclude unrelated profile matching.

## Verification

- Compare forwarder selection for multiple ASCII spellings of one name.

## Publication-Blockers

A retained standalone reproducer, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Recreate forwarder case reproducer
Action: Reconstruct the direct mixed-case resolver-selection comparison.
Done-When: Retain a runnable command with the configured rule, both names, and selected resolver results.
