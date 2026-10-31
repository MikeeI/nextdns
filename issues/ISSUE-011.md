# ISSUE-011 — router: configure errors do not prevent setup

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

Root-Cause [S]: The runner logs router Configure failures and still registers Setup.

## Reach-and-Impact

Reach [S]: UbiOS Configure rejects enabled content filtering before selecting its listener.
Impact [A]: Subsequent setup can direct dnsmasq to a listener that was not configured.

## Evidence

- [S] `run.go:214-227`; `router/ubios/setup.go:68-72,116-123`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Return Configure failures and do not run Setup after rejection.

## Scope-and-Constraints

- Preserve the platform compatibility check.
- Exclude unrelated router configuration.

## Verification

- Use an isolated UbiOS filter marker and verify setup does not run.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce rejected router setup
Action: Simulate enabled UbiOS content filtering during startup.
Done-When: Record listener and router setup effects.
