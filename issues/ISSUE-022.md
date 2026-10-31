# ISSUE-022 — host: failed resolver swap does not restore backup

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

Root-Cause [S]: A failed temporary-file rename returns after the original resolver file was moved to its backup.

## Reach-and-Impact

Reach [S]: Unix resolver-file activation uses the two-step rename.
Impact [A]: The resolver path can be absent after failed activation.

## Evidence

- [S] `host/dns_resolvconf.go:32-42`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Restore the backup when installation fails and report recovery errors.

## Scope-and-Constraints

- Preserve existing backup ownership and repeated activation semantics.
- Exclude unrelated resolver integrations.

## Verification

- Inject failure at the second rename and check both paths.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce resolver swap failure
Action: Fail the temporary resolver-file rename in isolation.
Done-When: Record original and backup file states.
