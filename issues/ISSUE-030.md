# ISSUE-030 — endpoint: DoH discovery reads response body only once

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: Medium
Root-Cause-Confidence: High
Finding-Category: Reliability
Created: 2026-10-31
Updated: 2026-10-31
Source: `upstream/master@63564a9733bcb214d9acfde8ba31357011617f4b`

## Root-Cause

Root-Cause [S]: DoH endpoint Exchange treats one body Read as the complete DNS response.

## Reach-and-Impact

Reach [S]: HTTPS service discovery parses the resulting bytes immediately.
Impact [A]: A valid fragmented response can fail discovery.

## Evidence

- [S] `resolver/endpoint/doh.go:73-94`; `resolver/endpoint/endpoint.go:260-270`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Read the response completely within a bounded buffer and identify overflow.

## Scope-and-Constraints

- Preserve cancellation and response-size bounds.
- Exclude regular resolver DoH body reading.

## Verification

- Serve a valid DNS response in multiple separate writes.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce fragmented discovery reply
Action: Serve a DNS discovery answer in multiple body fragments.
Done-When: Record the received bytes and discovery outcome.
