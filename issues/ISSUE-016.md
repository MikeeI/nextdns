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
Impact [O]: Saving and reloading a loopback-scoped profile made it match a non-loopback destination.
The addressless-interface branch remains source-proven, not runtime-observed.

## Evidence

- [S] `config/profile.go:51-57,90-118,162-170`.
- [S] `config/config.go:126-127` documents interface-scoped profiles.
- [S] Earlier imported review items `E18` and `E19` share the missing-condition representation.

## Bug-Reproduction

Environment: 2026-10-31; Go 1.27.3; Linux/amd64; source anchors match `Source`.
Command: `go run /tmp/nextdns-semantic-smoke.EhEc1u/main.go /tmp/nextdns-semantic-smoke.EhEc1u`.
Scenario: Save default and `lo=scoped` rules through `ConfigFileStorer`, reload, and compare destination matches.
Result [O]: `profile_roundtrip before=scoped encoded=[default scoped] after_loopback=scoped after_other=scoped`.
The 17-item semantic review retained this defect as `E7`, severity `High`.
The temporary harness was removed; reconstruct a standalone reproducer before publication.

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

A retained save/reload reproducer, addressless-interface execution, upstream prior art, direction, and target remain unresolved.

## Next-Action

Summary: Reproduce addressless interface rule
Action: Evaluate an interface-scoped profile before the interface has an address, without changing real networking.
Done-When: Record whether the rule is treated as default and retain the earlier save/reload scenario separately.
