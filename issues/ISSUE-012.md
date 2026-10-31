# ISSUE-012 — router: failed startup leaves prestart changes applied

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

Root-Cause [S]: Router Configure may mutate router DNS before listener startup, but Restore is only registered for Stop.

## Reach-and-Impact

Reach [S]: OpenWrt cache-mode configuration changes dnsmasq before binding.
Impact [A]: Startup failure can leave router DNS pointing away from a working service.

## Evidence

- [S] `run.go:63-70,214-235`; `router/openwrt/setup.go:43-53`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Track owned prestart mutations and roll them back when startup fails.

## Scope-and-Constraints

- Preserve administrator changes and handle partially applied configuration.
- Exclude normal successful shutdown changes.

## Verification

- Inject a listener bind failure after isolated router configuration.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce router startup rollback
Action: Fail listener binding after router Configure succeeds.
Done-When: Record before and after router state.
