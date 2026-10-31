# ISSUE-025 — service: subprocess output is read before copy completion

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

Root-Cause [S]: Concurrent pipe-copy goroutines are not joined before buffers are read after `Wait`.

## Reach-and-Impact

Reach [S]: Multiple service backends consume this helper's output.
Impact [A]: Status or error output can be incomplete and buffer access can race.

## Evidence

- [S] `host/service/internal/run_unix.go:20-49`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Synchronize output collection while preserving bounded subprocess and inherited-pipe lifetimes.

## Scope-and-Constraints

- Preserve the existing timeout and useful stderr.
- Exclude backend-specific status interpretation.

## Verification

- Exercise short commands and inherited pipes with race detection.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce service output race
Action: Run controlled subprocess output through the helper concurrently.
Done-When: Record race output and any missing bytes.
