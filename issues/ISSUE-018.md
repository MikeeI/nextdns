# ISSUE-018 — control: stop leaves accept loop and clients running

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

Root-Cause [S]: The accept loop retries after listener close and Stop does not close existing clients.

## Reach-and-Impact

Reach [S]: Server Stop closes the listener but clears only the client list.
Impact [A]: A persistent process can spin and retain client handlers after Stop.

## Evidence

- [S] `ctl/server.go:78-86,163-171`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Terminate the accept loop on listener close and close connected clients.

## Scope-and-Constraints

- Preserve valid event handling while running.
- Exclude control protocol changes.

## Verification

- Stop a server with a connected client and observe goroutines and connection closure.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce control server shutdown
Action: Stop an isolated control server with an active client.
Done-When: Record goroutine and client connection outcomes.
