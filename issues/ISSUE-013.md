# ISSUE-013 — OpenWrt: explicit port 53 does not enable port zero

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

Root-Cause [S]: Removing an explicitly configured default dnsmasq port does not set `SetPort0`.

## Reach-and-Impact

Reach [S]: The cache-mode template emits `port=0` only when `SetPort0` is true.
Impact [A]: dnsmasq can retain port 53 and prevent NextDNS from binding.

## Evidence

- [S] `router/openwrt/setup.go:81-101,224-229`.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Set port zero for an explicit default port and preserve the original UCI state.

## Scope-and-Constraints

- Preserve custom nondefault port behavior.
- Exclude unrelated DHCP options.

## Verification

- Compare absent, explicit 53, and custom ports through setup and restore.

## Publication-Blockers

Router reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce explicit dnsmasq port
Action: Configure an explicit dnsmasq port 53 in disposable OpenWrt.
Done-When: Record generated config, bind result, and restored UCI state.
