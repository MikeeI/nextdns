# ISSUE-038 — resolver: client discovery runs before fresh cache hits

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

Root-Cause [S]: `DOH.resolve` computes `ClientInfo` before checking whether a fresh cached response already satisfies the query.
The result is used only for outgoing HTTP headers and is discarded on a fresh cache hit.

## Reach-and-Impact

Reach [S]: UDP and TCP requests reach `resolver.DNS.Resolve` through the proxy.
With `cache-size` greater than zero, `report-client-info` enabled, and non-loopback clients, the reporting callback can synchronously reach reverse-DNS discovery.
Impact [O]: An isolated public-resolver smoke measured six fresh response-cache hits taking 100.24–100.33 ms each when client metadata was cold and discovery DNS discarded packets.
Impact [S]: Waiting for metadata retains a proxy request slot even though no DoH request is necessary.
Boundary: Production frequency, loaded latency distributions, and post-fix improvement were not measured.

## Evidence

- [S] `proxy/udp.go:89,137` and `proxy/tcp.go:89,138` parse and resolve incoming DNS queries.
- [S] `run.go:310-320,383-433,703-708` wires the response cache and LAN client-discovery callback.
- [S] `resolver/doh.go:59-84,95-105` invokes `ClientInfo` before cache lookup and uses its fields only for outgoing headers.
- [S] `discovery/dns.go:73-87,140-144,228-240` performs synchronous PTR discovery on metadata-cache misses, with a 100-ms socket deadline and five-minute caching.
- [O] `git ls-remote upstream HEAD refs/heads/master` returned the recorded source revision for both references.

## Performance-Evidence

Category: ShortCircuit.
Severity: High for the conditional user-visible request path, not a claim about all DNS traffic.
Cost model [Measured]: Six sequential requests through the real `resolver.DNS.Resolve`, using the real byte cache and `discovery.DNS.LookupAddr` in a narrow client-info callback.
The callback adapter exercised DNS name discovery, not the complete `setupClientReporting` implementation.
Environment: Go 1.27.3, Linux amd64; local HTTP/2 DoH responder and UDP blackhole in a separate network namespace.
The response cache and discovery initialization were warmed before measurement; each cold trial used a distinct client IP.
The same six IPs were then queried with warm metadata.

- [O] Cold metadata: `queries=6 cache_hits=6 clientinfo_calls=6 doh_requests=0 discovery_packets=6 min=100.239871ms median=100.310562ms max=100.332402ms`.
- [O] Warm metadata: `queries=6 cache_hits=6 clientinfo_calls=6 doh_requests=0 discovery_packets=0 min=751ns median=1.382µs max=4.147µs`.

Executed command: `unshare --net sh -c 'ip link set lo up && exec go run /tmp/nextdns-perf.ipE7ld/main.go /tmp/nextdns-perf.ipE7ld/measurement'`.
The temporary harness was removed after the smoke; this command records provenance and is not currently runnable without recreating it.
These are current-code baselines with different metadata-cache states, not a measured implementation improvement.

## Prior-Art

Coverage: Local finding titles and relevant symbols searched; no matching root cause found.
Related: `ISSUE-027` owns transient discovery errors being cached as negative answers, not eager metadata work.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research and callback-contract review.

## Proposed-Change

Check response-cache freshness before computing request-only client metadata.
Compute metadata only when an outgoing HTTP request is required, immediately before constructing its headers.

## Scope-and-Constraints

- Preserve profile selection, cache identity, configuration invalidation, stale fallback, device headers, and upstream admission policy.
- Review the exported callback's documented per-query invocation contract and any external consumers before changing invocation timing.
- Do not introduce background discovery, new caching, coalescing, or concurrent callbacks as part of this correction.
- Keep `ISSUE-039` separate: MAC-first name selection also avoids work on response-cache misses.

## Verification

- Recreate the isolated scenario and confirm fresh cache hits avoid client-info discovery and PTR packets.
- After authorized implementation, compare matched cache-hit workloads rather than claiming the warm/cold baseline as speedup.
- Smoke cache misses, stale fallback, profile invalidation, malformed upstream replies, and unchanged device headers.

## Publication-Blockers

A retained reproducer, callback-contract review, upstream prior art, user-selected work scope, and publication target remain unresolved.
No implementation or external publication is authorized by recording this finding.

## Next-Action

Summary: Recreate cache-hit discovery reproducer
Action: Recreate the isolated response-cache hit and cold client-discovery scenario as a retained research artifact.
Done-When: Record a runnable artifact and reproduce fresh cache hits with PTR traffic but no DoH traffic.
