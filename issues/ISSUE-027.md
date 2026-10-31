# ISSUE-027 — discovery: transient DNS errors become negative cache entries

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: Medium
Root-Cause-Confidence: High
Finding-Category: Reliability
Created: 2026-10-31
Updated: 2026-10-31
Source: `upstream/master@63564a9733bcb214d9acfde8ba31357011617f4b`

## Root-Cause

Root-Cause [S]: Discovery ignores lookup errors and caches empty results for five minutes.

## Reach-and-Impact

Reach [S]: Forward and reverse discovery lookups use this cache.
Impact [A]: Local names can remain unavailable after a transient upstream failure resolves.

## Evidence

- [S] `discovery/dns.go:73-87,95-114,140-144`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Distinguish lookup failures from authoritative negative responses before caching.

## Scope-and-Constraints

- Preserve intentional negative caching when justified.
- Handle partial A and AAAA failures explicitly.

## Verification

- Fail one lookup and retry immediately after upstream recovery.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce discovery negative caching
Action: Recover a controlled resolver immediately after a failed lookup.
Done-When: Record first and subsequent discovery results.
