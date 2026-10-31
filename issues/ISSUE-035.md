# ISSUE-035 — mDNS: cached addresses ignore TTL and goodbye records

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

Root-Cause [S]: mDNS parsing discards resource-record TTL and cached addresses are appended without expiry.

## Reach-and-Impact

Reach [S]: Forward and reverse discovery read the retained mappings.
Impact [A]: Address changes and goodbye records can leave stale names or addresses visible.

## Evidence

- [S] `discovery/mdns.go:217-235,266-305,315-319`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Honor record lifetime and goodbye semantics while maintaining both indexes.

## Scope-and-Constraints

- Preserve bounded storage and concurrent lookup safety.
- Assess cache-flush semantics against the mDNS protocol.

## Verification

- Send an address change and a TTL-zero goodbye and check both lookup directions.

## Publication-Blockers

Protocol reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce stale mDNS records
Action: Change an mDNS address and send its goodbye record.
Done-When: Record forward and reverse lookups before and after expiry.
