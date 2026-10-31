# ISSUE-046 — proxy: TCP frame prefixes allocate a temporary buffer

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

Root-Cause [S]: `readTCP` uses generic `binary.Read` for a two-byte frame prefix despite already owning a writable packet buffer.

## Reach-and-Impact

Reach [S]: `serveTCPConn` calls the helper for every accepted TCP frame using a pooled maximum-size buffer.
Impact [O]: Reading the prefix into that buffer removed one two-byte allocation for 32-, 512-, and 4096-byte payloads.
Boundary: The memory-reader fixture excludes socket latency and does not establish complete TCP-DNS throughput improvement.

## Evidence

- [S] `proxy/tcp.go:59-81,150-158` reads the prefix before the payload and rejects invalid or undersized production requests.
- [S] Symbol references found one production caller, `serveTCPConn`, which discards the buffer contents on read failure.
- [O] `BenchmarkMicroReadTCP` compares the real helper with resettable memory readers and preallocated packet buffers.
- [O] Temporary TCP result/error fingerprints matched for empty, partial-prefix, partial-payload, and complete frames.

## Performance-Evidence

Category: Reuse.
Severity: Low for measured framing work, not a claim about TCP network latency.
Method, source archive, raw data, reconstruction commands, and shared limitations: [measurement note](evidence/micro-optimizations-2026-10-31.md).

- [O] Go 1.26.4: 32-byte payload `28.43 -> 14.74 ns/op`; 4096-byte payload `55.98 -> 45.03 ns/op`.
- [O] Go 1.27.3: 32-byte payload `26.89 -> 13.61 ns/op`; 4096-byte payload `53.54 -> 43.19 ns/op`.
- [O] These four timing comparisons were significant at `p=0.002`, with six samples per variant.
- [O] All three payload sizes under both toolchains changed from `2 -> 0 B/op` and `1 -> 0 allocs/op`.
- [O] The Go-1.26 512-byte timing comparison was inconclusive (`p=0.394`); do not report its median as a proven speedup.

## Prior-Art

Coverage: Local records, broad upstream performance/allocation searches, and an exact-symbol PR query including `readTCP`; checked=2026-10-31.
Related: `ISSUE-003` owns admission lifetime and pending replies, not frame-prefix allocation.
Gaps: Broader framing terminology, discussions, and releases remain unchecked.
Contribution fit: A small internal framing optimization after error-path mutation and fragmented-reader review.

## Proposed-Change

For production-size buffers, use `io.ReadFull` into `buf[:2]`, decode with `binary.BigEndian.Uint16`, then read the payload as before.
The prototype retains the original path for buffers shorter than two bytes.

## Scope-and-Constraints

- Preserve frame lengths, EOF/UnexpectedEOF results, fragmented reads, admission policy, and pooled-buffer ownership.
- Prefix bytes now affect unused buffer contents after failed reads or successful payloads shorter than two bytes.
- The production caller discards those cases, but the private helper is not fully equivalent for arbitrary buffer observers or readers.
- Do not alter response framing, concurrent writes, packet-size limits, or known TCP admission defects.

## Verification

- [O] Temporary TCP fingerprints and proxy response smokes passed for the compound variant.
- Run an isolated differential smoke with fragmented readers, zero/one-byte payloads, and prefix/payload failures.
- Verify buffer mutation remains unobservable to every production caller and retain the short-buffer behavior deliberately.
- Measure real loopback TCP before making an end-to-end performance claim.

## Publication-Blockers

Isolated fragmented-read and error-mutation evidence, targeted prior art, work authorization, and publication target remain unresolved.

## Next-Action

Summary: Verify TCP prefix error semantics
Action: Compare prefix-only variants with fragmented readers and inspect buffer contents on every short-read outcome.
Done-When: Retain identical production-visible results and an explicit decision about unused private-buffer mutation.
