# ISSUE-016 — profiles: interface condition is not represented durably

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

Root-Cause [S]: An interface profile stores only resolved destination addresses, not the interface condition.

## Reach-and-Impact

Reach [S]: Profile serialization omits the condition and an empty address set is treated as default.
Impact [A]: Saving or starting before address assignment can turn an interface rule into a global profile.

## Evidence

- [S] `config/profile.go:51-57,90-118,162-170`.
- [S] `config/config.go:126-127` documents interface-scoped profiles.
- [S] Previous review items `E18` and `E19` share the missing-condition representation.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Preserve the interface condition and do not treat absent addresses as a default rule.

## Scope-and-Constraints

- Preserve interface-specific matching and profile ordering.
- Exclude other profile condition types.

## Verification

- Save and reload an interface rule and evaluate it while the interface has no addresses.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce interface profile loss
Action: Exercise profile save and an addressless interface.
Done-When: Record serialized rules and matching outcomes.
