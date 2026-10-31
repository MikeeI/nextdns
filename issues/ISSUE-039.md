# ISSUE-039 — reporting: reverse lookup precedes overriding MAC name

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: High
Root-Cause-Confidence: High
Finding-Category: Performance
Created: 2026-10-31
Updated: 2026-10-31
Source: `upstream/master@63564a9733bcb214d9acfde8ba31357011617f4b`

## Root-Cause

Root-Cause [S]: `setupClientReporting` computes the client's IP-derived name before looking up the preferred MAC-derived name.
A nonempty MAC-name result overwrites the already computed IP-name result completely.

## Reach-and-Impact

Reach [S]: With `report-client-info` enabled for non-loopback clients, the DoH callback resolves names through the configured discovery sources.
MAC-only router sources can know a device name even when earlier sources do not know its current IP name.
Impact [S]: The IP lookup is redundant whenever `LookupMAC` subsequently returns names.
Impact [Derived]: If that lookup reaches a cold reverse-DNS entry and an unresponsive discovery server, one unnecessary PTR operation incurs the 100-ms socket deadline.
Boundary: This also affects response-cache misses and cache-disabled operation; the full MAC-hit reporting path was not executed or timed.

## Evidence

- [S] `run.go:383-433` enables reporting discovery for non-loopback listening and registers router, DHCP, mDNS, and DNS sources.
- [S] `run.go:703-729` computes `LookupAddr` first, then replaces `ci.Name` when `LookupMAC` returns names.
- [S] `discovery/resolver.go:29-36,49-58` preserves source precedence independently for IP and MAC lookups.
- [S] `discovery/ubios_linux.go:77-85` exposes MAC names while `LookupAddr` always returns `nil`.
- [S] `discovery/merlin_linux.go:66-74` has the same MAC-only name interface.
- [S] `discovery/dns.go:73-87,228-240` performs PTR I/O on metadata-cache misses with a 100-ms socket deadline.

## Performance-Evidence

Category: ShortCircuit.
Severity: High for the conditional user-visible request path, not a claim about every reported client.
Cost model [Derived]: One fully computed and discarded `LookupAddr` per reporting callback with a subsequent MAC-name hit.
The costly case requires missing IP names in earlier sources, a cold DNS-discovery cache entry, and slow or unresponsive discovery DNS.
`ISSUE-038` records a smoke of the same native DNS-discovery operation taking 100.24–100.33 ms against a UDP blackhole.
That measurement establishes the shared operation's cost, not an observed UbiOS/Merlin reporting delay or a measured fix improvement.
Production frequency and loaded latency distributions are unknown.

## Prior-Art

Coverage: Local finding titles and relevant symbols searched; no matching root cause found.
Related: `ISSUE-038` owns metadata being computed before response-cache hits; this cause remains after that optimization.
Related: `ISSUE-027` owns negative caching after transient discovery errors, not name-selection order.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research and end-to-end MAC-hit reproduction.

## Proposed-Change

When a MAC address is available, check `LookupMAC` before resolving the IP-derived name.
For `len(names) > 0`, retain the existing normalization and MAC-name precedence without performing `LookupAddr`.
Otherwise preserve the existing IP-name lookup and alternate-IP fallback.

## Scope-and-Constraints

- Preserve IDs, profile selection, source precedence, name normalization, model headers, and behavior without a MAC address.
- Branch on `len(names) > 0`, not on whether normalization produces a nonempty string.
- Preserve `clientIPFromMAC` fallback for missing MAC names and an empty IP-derived name, including IPv6 link-local clients.
- Do not parallelize name sources or alter freshness, failure caching, or synchronization contracts.
- Keep the response-cache ordering correction in `ISSUE-038` independently reviewable.

## Verification

- Reproduce a known MAC name plus an unknown IP name against blackholed discovery DNS and count PTR packets.
- After authorized implementation, compare matched metadata states and device headers; MAC-name hits should avoid PTR I/O.
- Smoke absent MAC addresses, missing MAC names, empty normalized names, and alternate-IP fallback.

## Publication-Blockers

The complete MAC-hit reporting scenario, upstream prior art, user-selected work scope, and publication target remain unresolved.
No implementation or external publication is authorized by recording this finding.

## Next-Action

Summary: Reproduce discarded reverse lookup
Action: Run the reporting callback with a known MAC name, unknown IP name, and a controlled discovery-DNS blackhole.
Done-When: Record the emitted MAC-derived device name, redundant PTR traffic, and callback duration.
