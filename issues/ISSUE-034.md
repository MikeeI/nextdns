# ISSUE-034 — service: listener failure can be reported as startup success

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

Root-Cause [S]: The startup goroutine sends listener errors on an unbuffered channel with a default discard and startup returns success after five seconds.

## Reach-and-Impact

Reach [S]: Listener failure ends the proxy listeners.
Impact [A]: The service can appear started while DNS listeners are unavailable.

## Evidence

- [S] `run.go:92-115`; `proxy/proxy.go:120-170`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Signal listener readiness explicitly and propagate errors through the service lifecycle.

## Scope-and-Constraints

- Preserve cancellation and startup failure cleanup.
- Router rollback is tracked separately in `ISSUE-012`.

## Verification

- Inject listener errors before and after the current startup window.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce lost listener error
Action: Trigger early and late listener failures in an isolated service.
Done-When: Record startup result and service availability.
