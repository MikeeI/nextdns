# ISSUE-007 — cache: DNS53 upstreams share an empty namespace

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

Root-Cause [S]: DNS53 cache keys omit upstream identity despite a shared cache.

## Reach-and-Impact

Reach [S]: Resolver and forwarder instances can share one cache and endpoints can change.
Impact [A]: A still-fresh answer from another upstream or network can survive a switch.

## Evidence

- [S] `resolver/dns53.go:40,90` uses an empty cache context.
- [S] `run.go:308-320,439-451` shares the cache across resolvers.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Separate cache entries by upstream and handle network changes that alter answer context.

## Scope-and-Constraints

- Preserve valid cache hits within an unchanged DNS context.
- Exclude unrelated DoH request identity.

## Verification

- Switch between controlled upstreams returning different answers for the same question.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce upstream cache collision
Action: Query controlled DNS53 endpoints before and after a switch.
Done-When: Record endpoints, TTLs, and returned wire answers.
