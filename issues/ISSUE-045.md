# ISSUE-045 — proxy: UDP ancillary buffer is allocated per receive

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

Root-Cause [S]: `readUDP` creates a new `udpOOBSize` ancillary buffer for each receive instead of reusing listener-owned storage.

## Reach-and-Impact

Reach [S]: The serial listener loop calls `readUDP` for each receive attempt before request dispatch.
Impact [O]: Listener-local reuse removed one allocation and approximately 48 B per packet in Linux IPv4 and IPv6 loopback cases.
Boundary: Neither toolchain showed a significant UDP roundtrip-latency gain; router GC, throughput, and resident memory are unmeasured.

## Evidence

- [S] `proxy/udp.go:25-39,61-64,192-200` sizes, creates, and consumes the ancillary buffer.
- [S] `proxy/udp_unix.go:25-35` parses destination addresses synchronously before worker dispatch.
- [S] x/net v0.61.0 `ipv4/control_pktinfo.go:34-40` and `ipv6/control_rfc3542_unix.go:61-67` copy destinations into separate slices.
- [O] `BenchmarkMicroUDP` used real IPv4/IPv6 loopback sockets, local Hosts answers, and the production discovery adapter.

## Performance-Evidence

Category: Reuse.
Severity: Low for measured allocation pressure without a demonstrated latency improvement.
Method, source archive, raw data, reconstruction commands, and shared limitations: [measurement note](evidence/micro-optimizations-2026-10-31.md).

- [O] Go 1.26.4 IPv4: `14 -> 13 allocs/op`, `735.0 -> 686.5 B/op`, and `17.51 -> 17.33 us/op` (`p=0.394`).
- [O] Go 1.26.4 IPv6: `12 -> 11 allocs/op`, `769 -> 721 B/op`, and `16.90 -> 16.90 us/op` (`p=0.699`).
- [O] Go 1.27.3 IPv4: `15 -> 14 allocs/op`, `751 -> 703 B/op`, and `17.64 -> 17.68 us/op` (`p=0.937`).
- [O] Go 1.27.3 IPv6: `13 -> 12 allocs/op`, `785 -> 737 B/op`, and `17.18 -> 17.14 us/op` (`p=0.310`).

Each comparison used six samples per variant.
The compound prototype contains other changes; its latency cannot be attributed solely to ancillary-buffer reuse.

## Prior-Art

Coverage: Local records, broad upstream performance/allocation searches, and an exact-symbol PR query including `readUDP`; checked=2026-10-31.
No matching root cause was found in the covered results.
Gaps: Broader ancillary-buffer terminology, discussions, and releases remain unchecked.
Contribution fit: A bounded listener-ownership optimization after isolated measurement and platform-lifetime review.

## Proposed-Change

Allocate the OOB buffer once after socket setup and pass it to `readUDP` on each serial receive.
Continue parsing only `oob[:oobn]` and pass workers only destination-address storage independent of that reusable buffer.

## Scope-and-Constraints

- Document the pinned x/net destination-copy dependency beside the reusable buffer.
- Preserve independent request and response buffers, admission control, malformed packets, timeout handling, and shutdown.
- Do not reuse an outgoing control-message buffer across workers or replace platform-native control-message parsing.
- Windows returns no local destination; Linux smokes do not establish BSD, macOS, AIX, or Solaris runtime behavior.

## Verification

- [O] Temporary IPv4/IPv6 response smokes and a Go-1.27.3 targeted proxy race smoke passed.
- [O] The experimental root, proxy, and query packages cross-compiled for ARMv7, MIPSLE softfloat, and Windows/amd64.
- Isolate OOB reuse and exercise concurrent receives, varying destinations, short packets, timeouts, and rejected requests.
- Review destination ownership for every supported platform before claiming cross-platform runtime safety.

## Publication-Blockers

An isolated OOB-only comparison, complete platform-ownership review, targeted prior art, work authorization, and publication target remain unresolved.

## Next-Action

Summary: Validate UDP ancillary buffer ownership
Action: Exercise OOB-only reuse with concurrent receives and varying local destinations while reviewing supported platform parsers.
Done-When: Retain source-address-correct responses and evidence that no worker retains overwritten OOB storage.
