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
Impact [A]: Equivalent DNS names can select different forwarders.

## Evidence

- [S] `config/forwarder.go:33-39,50-58`; `resolver/query/query.go:156-165`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Compare DNS names case-insensitively while preserving label boundaries.

## Scope-and-Constraints

- Preserve forwarder precedence and domain-boundary semantics.
- Exclude unrelated profile matching.

## Verification

- Compare forwarder selection for multiple ASCII spellings of one name.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce forwarder case mismatch
Action: Query equivalent mixed-case domain names against configured forwarding.
Done-When: Record selected upstreams for each spelling.
