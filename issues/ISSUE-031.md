# ISSUE-031 — endpoint: DoH URLs lose their configured port

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

Root-Cause [S]: The parsed URL host includes the port while transport address construction always appends port 443.

## Reach-and-Impact

Reach [S]: Configured HTTPS forwarders use endpoint URL parsing.
Impact [A]: Nondefault-port DoH endpoints fail to connect correctly.

## Evidence

- [S] `resolver/endpoint/endpoint.go:57-70`; `resolver/endpoint/transport_h2.go:19-25,82-89`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Represent hostname and port separately and use the configured port with bootstrap addresses.

## Scope-and-Constraints

- Preserve TLS hostname verification and default port behavior.
- URL query handling is a separate finding.

## Verification

- Connect to a controlled nondefault-port DoH server with and without bootstrap.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce nondefault DoH port
Action: Query a DoH endpoint listening on a nondefault port.
Done-When: Record destination, TLS name, and connection outcome.
