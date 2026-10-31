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
Impact [O]: After listener close, the accept loop continued producing errors in the still-running smoke process.
Impact [A]: Existing client handlers can remain active; their retention has not been runtime-tested.
The 17-item review retained accept-loop item `E17` as `Low`; contribution priority also covers untested client cleanup.

## Evidence

- [S] `ctl/server.go:78-86,163-171`.

## Bug-Reproduction

Environment: 2026-10-31; Go 1.27.3; Linux/amd64; source anchors match `Source`.
Command: `go run /tmp/nextdns-semantic-smoke.EhEc1u/main.go /tmp/nextdns-semantic-smoke.EhEc1u`.
Scenario: Close the smoke client, stop its control server, and count subsequent accept errors for about 30 ms.
Result [O]: `control_stop accept_errors_after_stop=599440`.
The measured count is not a production CPU-utilization or log-volume measurement.
The temporary harness was removed; reconstruct a standalone reproducer before publication.

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

A retained accept-loop reproducer, active-client verification, upstream prior art, direction, and target remain unresolved.

## Next-Action

Summary: Reproduce retained control clients
Action: Stop an isolated server while a client remains connected and observe handler termination.
Done-When: Record client closure and goroutine outcomes separately from the observed accept-loop spin.
