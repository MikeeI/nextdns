# ISSUE-015 — Merlin: active NextDNS skips existing postconf script

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

Root-Cause [S]: The generated NextDNS block exits before the appended original postconf script.

## Reach-and-Impact

Reach [S]: Existing postconf content is appended after the NextDNS block.
Impact [A]: Administrator DNS or DHCP customizations do not run while NextDNS is active.

## Evidence

- [S] `router/merlin/setup.go:51-70,106-132`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Execute the existing postconf in the active case with deliberate ordering.

## Scope-and-Constraints

- Preserve existing script semantics and NextDNS modifications.
- Exclude unrelated Merlin service setup.

## Verification

- Run an isolated generated postconf with an observable existing-script effect.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce skipped postconf script
Action: Exercise an existing postconf while the NextDNS PID is active.
Done-When: Record the script execution order and observed effect.
