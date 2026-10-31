# ISSUE-024 — activation: persisted mode ignores operation and save failures

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

Root-Cause [S]: Activation changes `AutoActivate` before the host operation and ignores deferred Save errors.

## Reach-and-Impact

Reach [S]: Both activate and deactivate use the same deferred save.
Impact [A]: Persistent intent can diverge from the actual host DNS state after failure.

## Evidence

- [S] `activate.go:14-25`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Define explicit failure and recovery behavior for host changes and persistence.

## Scope-and-Constraints

- Preserve successful activation and deactivation behavior.
- Avoid claiming atomicity without an actual recovery mechanism.

## Verification

- Inject host-operation and save failures independently and compare both states.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce activation state divergence
Action: Fail host DNS changes and configuration saves separately.
Done-When: Record command result, host state, and stored mode.
