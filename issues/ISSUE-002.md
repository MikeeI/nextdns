# ISSUE-002 — OpenWrt: forwarder recovery depends on process memory

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

Root-Cause [S]: UCI forwarders are committed as deleted after their values are saved only in process memory.

## Reach-and-Impact

Reach [S]: OpenWrt setup without the NextDNS cache changes dnsmasq forwarders.
Impact [A]: A crash before restore can prevent recovery of original forwarders.

## Evidence

- [S] `router/openwrt/setup.go:103-116,165-178` saves `savedForwarders` in memory and uses it for restore.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Journal original forwarders before committing UCI changes and recover them after restart.

## Scope-and-Constraints

- Preserve unrelated administrator changes and exact forwarder values.
- Exclude unrelated router integrations.

## Verification

- In disposable OpenWrt, terminate after commit and verify recovery across restart.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce forwarder recovery failure
Action: Reproduce process termination between UCI commit and restore.
Done-When: Record the original, interrupted, and recovered UCI states.
