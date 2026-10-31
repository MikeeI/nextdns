# ISSUE-032 — Linux: gateway fallback parses route fields incorrectly

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

Root-Cause [S]: Route detection compares ASCII hexadecimal fields to binary zero bytes and assumes the same field layout for IPv4 and IPv6.

## Reach-and-Impact

Reach [S]: Host DNS discovery includes gateway fallback sources.
Impact [A]: Available gateway DNS may be missed when other discovery sources fail.

## Evidence

- [S] `host/dns_linux.go:20-55,170-200`; `run.go:645-667`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Decode and identify default routes according to each Linux route format.

## Scope-and-Constraints

- Preserve existing fallback ordering and DNS probing.
- Do not claim that all fallback DNS is absent.

## Verification

- Evaluate representative IPv4 and IPv6 default and nondefault route rows.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce gateway route parsing
Action: Evaluate controlled Linux route rows for both address families.
Done-When: Record parsed gateways and expected results.
