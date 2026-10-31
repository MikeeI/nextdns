# ISSUE-019 — discovery: failed semaphore acquisition releases another owner

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

Root-Cause [S]: `runSingle` defers release even when acquisition failed.

## Reach-and-Impact

Reach [S]: The semaphore is intended to prevent concurrent recursive lookups for one key.
Impact [A]: A later lookup can enter while the original owner is still running.

## Evidence

- [S] `discovery/dns.go:117-126`; `discovery/semaphore.go:10-26`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Release the key only after successful acquisition.

## Scope-and-Constraints

- Preserve DNS loop prevention.
- Exclude unrelated lookup caching.

## Verification

- Block one lookup and execute two further lookups of the same key.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce discovery semaphore ownership
Action: Hold one key while attempting two more acquisitions.
Done-When: Record whether the third acquisition enters prematurely.
