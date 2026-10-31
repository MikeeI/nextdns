# ISSUE-023 — service: install removes the old service before validation

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

Root-Cause [S]: Install stops and uninstalls before validating and saving the replacement, then prints success regardless of install or start error.

## Reach-and-Impact

Reach [S]: The install path uses service-specific stop, uninstall, save, install, and start operations.
Impact [A]: A failed replacement can leave a formerly working service unavailable and print a misleading status.

## Evidence

- [S] `service.go:16-22,39-54`; `config/config.go:42-58`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Validate before removal, handle operation errors, and print success only after successful start.

## Scope-and-Constraints

- Preserve supported init-system behavior.
- Full cross-platform rollback needs separate feasibility assessment.

## Verification

- Inject validation, installation, and startup failures in an isolated service backend.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce service replacement failure
Action: Exercise invalid configuration and failing service installation.
Done-When: Record old service state and command output.
