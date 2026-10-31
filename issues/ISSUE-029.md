# ISSUE-029 — netstatus: address changes lack historical snapshots

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

Root-Cause [S]: Address comparison queries current addresses for both stored and current interface values.

## Reach-and-Impact

Reach [S]: Interface status notifications trigger endpoint retesting and reset the fallback window.
Impact [A]: Address-only changes may not trigger those actions.

## Evidence

- [S] `netstatus/netstatus.go:92-102,143-145`; `run.go:506-524`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Capture interface addresses as part of each comparable snapshot.

## Scope-and-Constraints

- Preserve existing interface flag change detection.
- Exclude unrelated network monitoring redesign.

## Verification

- Change an interface address without changing flags in an isolated network namespace.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce missed address change
Action: Change only an interface address while subscribed to notifications.
Done-When: Record interface state and received notifications.
