# ISSUE-043 — reporting: peer IP is formatted twice per callback

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: Low
Root-Cause-Confidence: High
Finding-Category: Performance
Created: 2026-10-31
Updated: 2026-10-31
Source: `upstream/master@63564a9733bcb214d9acfde8ba31357011617f4b`

## Root-Cause

Root-Cause [S]: The non-loopback reporting callback formats `q.PeerIP` twice consecutively instead of reusing `ci.IP`.

## Reach-and-Impact

Reach [S]: DoH calls the callback when `report-client-info` is enabled; it is disabled by default.
Impact [O]: Reusing the first string removed one allocation and 16 B per callback in the measured IPv4 and IPv6 cases.
Boundary: The fixture used an empty discovery resolver and no MAC; complete DoH latency and router impact are unmeasured.

## Evidence

- [S] `run.go:703-708` assigns `ci.IP` and then formats the unchanged IP again for `LookupAddr`.
- [S] `resolver/doh.go:59-62` invokes the callback; `config/config.go:166-167` defines the reporting default.
- [O] `BenchmarkMicroClientInfo` exercised the real callback; its final root-file variant changes only IP-string reuse.
- [O] Both variants produced the same `TestMicroIDFingerprint`, including IPv4, mapped IPv4, IPv6, nil, and unusual IP lengths.

## Performance-Evidence

Category: AvoidConversion.
Severity: Low for measured local work, not a claim about all DNS traffic.
Method, source archive, raw data, reconstruction commands, and shared limitations: [measurement note](evidence/micro-optimizations-2026-10-31.md).

- [O] Go 1.26.4: IPv4 `137.6 -> 106.5 ns/op`; IPv6 `223.0 -> 156.5 ns/op`.
- [O] Go 1.27.3: IPv4 `145.9 -> 102.4 ns/op`; IPv6 `234.5 -> 163.0 ns/op`.
- [O] Both toolchains: IPv4 `32 -> 16 B/op`, IPv6 `37 -> 21 B/op`, and `3 -> 2 allocs/op`.
- [O] All four timing comparisons were significant at `p=0.002`, with six samples per variant.

## Prior-Art

Coverage: Local records and broad upstream performance/allocation searches; checked=2026-10-31.
Related: `ISSUE-038` owns eager callback execution on cache hits; `ISSUE-039` owns discarded IP-name discovery before MAC names.
This cause preserves both callback timing and lookup order and is not a duplicate of either record.
Gaps: A dedicated upstream IP-formatting search, discussions, and releases remain unchecked.
Contribution fit: A one-line performance correction after targeted prior-art and representative workload review.

## Proposed-Change

Keep `ci.IP = q.PeerIP.String()` and pass `ci.IP` to `r.LookupAddr`.

## Scope-and-Constraints

- Preserve lookup order, normalization, MAC-name precedence, profile selection, device IDs, and loopback behavior.
- Preserve `net.IP.String` output for nil, mapped IPv4, IPv6, and unusual IP lengths.
- Do not move discovery, add caches, or change `shortID` as part of this correction.
- Treat the archived compound prototype as measurement evidence, not an upstream-ready patch.

## Verification

- [O] Temporary ID/client fingerprints matched under Go 1.26.4 and Go 1.27.3.
- Compare an IP-reuse-only variant with MAC-present and warm real discovery sources before a broader performance claim.
- Measure native router workloads before claiming router throughput, GC, or memory-footprint improvement.

## Publication-Blockers

Targeted upstream prior art, a representative full-callback comparison, work authorization, and publication target remain unresolved.

## Next-Action

Summary: Measure complete reporting workload
Action: Compare IP-string reuse with the complete callback using MAC-present and warm discovery-source cases.
Done-When: Retain identical ClientInfo outputs and matched allocation/timing results beyond the empty-source fixture.
