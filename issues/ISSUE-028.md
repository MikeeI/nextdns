# ISSUE-028 — hosts: localhost fallback uses an unnormalized key

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

Root-Cause [S]: The fallback stores `localhost` while host lookups normalize the name with a trailing dot.

## Reach-and-Impact

Reach [S]: The fallback is intended for empty system hosts files.
Impact [A]: Localhost lookup and default-listener activation can fail in that environment.

## Evidence

- [S] `discovery/hosts.go:67-71,125-140`; `activate.go:53-60`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Store fallback entries under the same canonical keys used for lookup.

## Scope-and-Constraints

- Preserve administrator-defined localhost mappings.
- Exclude unrelated hosts-file precedence.

## Verification

- Use an empty hosts file and look up `localhost`.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce localhost fallback failure
Action: Load an empty hosts file and resolve localhost.
Done-When: Record forward lookup and activation outcomes.
