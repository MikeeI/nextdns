# ISSUE-021 — Windows: DNS reset discards interface errors

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

Root-Cause [S]: ResetDNS returns nil after collecting IPv4 errors and ignores IPv6 reset errors.

## Reach-and-Impact

Reach [S]: Windows deactivation invokes this reset path.
Impact [A]: DNS settings can remain configured while deactivation reports success.

## Evidence

- [S] `host/dns_windows.go:32-42,54-59`; `activate.go:23-25`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Collect and return IPv4 and IPv6 reset errors.

## Scope-and-Constraints

- Preserve reset attempts for every interface.
- Exclude unrelated Windows activation behavior.

## Verification

- Fail IPv4 and IPv6 netsh calls independently and check returned errors.

## Publication-Blockers

Windows reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce Windows reset errors
Action: Cause individual netsh reset operations to fail in isolation.
Done-When: Record exit status and remaining DNS configuration.
