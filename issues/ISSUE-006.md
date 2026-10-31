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
Impact [A]: A DNSSEC-capable query can receive a response cached for a different request.

## Evidence

- [S] `resolver/cache.go:13-37` keys on context, class, type, and name only.
- [S] `resolver/dns53.go:37-47` and `resolver/doh.go:73-85` consume those keys.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Include answer-relevant request semantics in cache identity or bypass unsafe queries.

## Scope-and-Constraints

- Preserve profile separation and DNS wire semantics.
- Exclude unrelated cache replacement policies.

## Verification

- Compare cached and uncached queries with differing DO, RD, and relevant EDNS options.

## Publication-Blockers

Wire reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce query cache collision
Action: Compare wire answers for the same question with different DNS semantics.
Done-When: Record the requests, upstream answers, and cache-hit answers.
