# ISSUE-042 — OpenWrt: DHCP DNS membership accepts address substrings

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: Medium
Root-Cause-Confidence: High
Finding-Category: Correctness
Created: 2026-10-31
Updated: 2026-10-31
Source: `upstream/master@63564a9733bcb214d9acfde8ba31357011617f4b`

## Root-Cause

Root-Cause [S]: `ensureDHCPOption` uses substring matching to decide whether the router DNS option already exists.
An address prefix can match a different configured DNS address and skip the required update.

## Reach-and-Impact

Reach [S]: The OpenWrt setup path expects DHCP option 6 to advertise the router's IPv4 address.
Impact [S]: With expected `6,192.168.1.1`, an existing `6,192.168.1.10` makes the helper return without adding it.
Impact [A]: Clients can retain the wrong DNS server; an OpenWrt DHCP exchange has not been executed.
The 17-item semantic review retained this source-proven defect as `E16`, severity `Medium`.

## Evidence

- [S] `router/openwrt/setup.go:143-151` builds `expectedOption` and returns on `strings.Contains`.
- [S] The distinct values `6,192.168.1.1` and `6,192.168.1.10` satisfy that substring predicate.

## Prior-Art

Coverage: Local ledger searched; `ISSUE-014` owns restore ownership, not membership comparison.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Compare complete UCI DHCP-option entries rather than accepting a substring inside a different address.

## Scope-and-Constraints

- Preserve unrelated DHCP options and existing UCI list representation.
- Exclude the distinct restore-ownership correction tracked in `ISSUE-014`.
- Do not claim client-visible packet behavior before a disposable OpenWrt reproduction.

## Verification

- Use router address `192.168.1.1` and existing option `6,192.168.1.10`; verify the helper does not skip setup.
- Verify an exact existing match is accepted without duplicate insertion.

## Publication-Blockers

OpenWrt reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce DHCP substring match
Action: Exercise setup with prefix-overlapping DNS addresses in disposable OpenWrt.
Done-When: Record UCI values before and after setup and the DNS address advertised to a client.
