# ISSUE-037 — forwarder: DoH URL query parameters are not preserved

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

Root-Cause [S]: Forwarder parsing treats the first equals sign as a domain separator and endpoint parsing discards URL query data.

## Reach-and-Impact

Reach [S]: HTTPS URLs are accepted as forwarder addresses.
Impact [A]: Query-based routing parameters can be misparsed or omitted from requests.

## Evidence

- [S] `config/forwarder.go:20-26`; `resolver/endpoint/endpoint.go:57-66`; `resolver/endpoint/transport_h2.go:73-79`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Distinguish domain conditions from URL query syntax and preserve the request query.

## Scope-and-Constraints

- Preserve existing domain-conditioned forwarder syntax and bootstrap behavior.
- Nondefault port handling is tracked separately in `ISSUE-031`.

## Verification

- Send DoH URLs with and without equals signs in their queries to a controlled server.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce DoH query loss
Action: Exercise conditional and unconditional DoH URLs with query parameters.
Done-When: Record parse results and received request targets.
