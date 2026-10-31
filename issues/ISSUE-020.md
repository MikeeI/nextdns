# ISSUE-020 — discovery: hosts refresh mutates state under read lock

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: High
Root-Cause-Confidence: High
Finding-Category: Reliability
Created: 2026-10-31
Updated: 2026-10-31
Source: `upstream/master@63564a9733bcb214d9acfde8ba31357011617f4b`

## Root-Cause

Root-Cause [S]: Hosts lookup and visit call a mutating refresh while holding only `RLock`.

## Reach-and-Impact

Reach [S]: Concurrent lookups can race on expiry and map references.
Impact [A]: Discovery results can become inconsistent; a specific map panic is not established.

## Evidence

- [S] `discovery/hosts.go:30-44,51-71,83-95`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Synchronize refresh exclusively and publish completed snapshots.

## Scope-and-Constraints

- Preserve concurrent reads and host-file behavior.
- Assess other discovery sources separately before expanding scope.

## Verification

- Run concurrent lookups and file refresh under the race detector.

## Publication-Blockers

Race reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce hosts refresh race
Action: Trigger parallel hosts lookups while refreshing the file.
Done-When: Record race detector output and affected state.
