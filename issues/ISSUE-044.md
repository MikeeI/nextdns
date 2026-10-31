# ISSUE-044 — proxy: hosts misses allocate a discarded error

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

Root-Cause [S]: `hostsResolve` constructs a fresh `errors.New("not found")` for every miss, while its callers discard that error.

## Reach-and-Impact

Reach [S]: `Proxy.Resolve` attempts local Hosts resolution when `use-hosts` is enabled, which is the default.
The same helper also handles conditional discovery lookups.
Impact [O]: A private sentinel removed one allocation and 16 B per miss in the isolated helper benchmark.
Boundary: Public proxy-miss allocation costs, production miss rates, and native router impact remain unmeasured.

## Evidence

- [S] `proxy/util.go:39-71` constructs the miss error after unsuccessful local lookup.
- [S] `proxy/proxy.go:175-191` checks only whether the helper error is nil and discards unsuccessful results.
- [S] `config/config.go:190-191` enables Hosts lookup by default; `run.go:378-380` wires the source.
- [O] `BenchmarkMicroHostsMiss` uses the repository's `discovery.Dummy` and retains the helper error in a benchmark sink.

## Performance-Evidence

Category: Reuse.
Severity: Low for measured local work, without an established end-to-end latency effect.
Method, source archive, raw data, reconstruction commands, and shared limitations: [measurement note](evidence/micro-optimizations-2026-10-31.md).

- [O] Go 1.26.4: `34.28 -> 14.47 ns/op`.
- [O] Go 1.27.3: `24.79 -> 14.22 ns/op`.
- [O] Both toolchains: `16 -> 0 B/op` and `1 -> 0 allocs/op`.
- [O] Both timing comparisons were significant at `p=0.002`, with six samples per variant.

The fixture isolates error construction and retains the result; it does not prove the allocation cost of every public caller context.

## Prior-Art

Coverage: Local finding records and broad upstream performance/allocation searches; checked=2026-10-31.
No matching root cause was found in that coverage.
Gaps: A dedicated discarded-error or `hostsResolve` upstream search, discussions, and releases remain unchecked.
Contribution fit: A small private-error reuse candidate after a public-boundary allocation comparison and prior-art review.

## Proposed-Change

Define one private package-level `errHostNotFound = errors.New("not found")` and return it for the existing miss branch.

## Scope-and-Constraints

- Preserve the error text and nil/non-nil outcomes; known production callers do not observe miss-error identity.
- Preserve parsing errors, upstream errors, source order, and the distinction between a miss and a filtered empty answer.
- Do not expose a new error API, alter Hosts filtering, or introduce small-list reserves or normalization fast paths.
- Recheck all helper consumers before implementation because sentinel reuse intentionally changes private error identity.

## Verification

- Reproduce the allocation difference through `Proxy.Resolve` with a real local-source miss and a controlled valid upstream answer.
- Confirm successful local answers and discovery fallback remain byte-identical.
- Keep malformed-query and upstream errors separate from the private miss sentinel.

## Publication-Blockers

A public-boundary miss comparison, targeted upstream prior art, work authorization, and publication target remain unresolved.

## Next-Action

Summary: Measure realistic hosts misses
Action: Compare original and sentinel-only variants through the public Proxy.Resolve miss path.
Done-When: Retain matched responses, errors, and allocation results without forcing the helper error to escape through a sink.
