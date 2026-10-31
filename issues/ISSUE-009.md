# ISSUE-009 — resolver: DNS53 does not retry truncated replies over TCP

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

Root-Cause [S]: DNS53 always uses UDP and does not retry a truncated response over TCP.

## Reach-and-Impact

Reach [S]: DNS53 forwarders are advertised as supporting UDP and TCP.
Impact [A]: Large answers can remain truncated even for TCP clients.

## Evidence

- [S] `config/config.go:139-143`; `resolver/dns53.go:58-81`; `proxy/tcp.go:138-141`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Retry truncated upstream DNS53 responses over TCP.

## Scope-and-Constraints

- Preserve existing endpoint selection and DNS framing.
- Exclude unrelated DoH behavior.

## Verification

- Compare controlled UDP truncation with the upstream TCP answer.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce DNS53 truncation failure
Action: Query an upstream serving truncated UDP and complete TCP answers.
Done-When: Record proxy and upstream wire responses.
