# ISSUE-014 — OpenWrt: restore removes DHCP options it did not add

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

Root-Cause [S]: Restore deletes the current router DNS option without recording whether setup inserted it.

## Reach-and-Impact

Reach [S]: Existing matching DHCP options are accepted without modification.
Impact [A]: Normal start and stop can remove administrator-owned DHCP configuration.

## Evidence

- [S] `router/openwrt/setup.go:143-151,183-193`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Record ownership and the exact inserted value; remove only owned changes.

## Scope-and-Constraints

- Preserve existing options and handle LAN address changes.
- Exclude unrelated UCI keys.

## Verification

- Exercise existing option and changed LAN address across setup and restore.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce DHCP option deletion
Action: Start and stop with a preexisting matching DHCP option.
Done-When: Record UCI values before and after restore.
