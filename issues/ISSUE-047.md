# ISSUE-047 — proxy: private PTR classification runs with policy disabled

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

Root-Cause [S]: `Proxy.Resolve` classifies private reverse names even when `BogusPriv` is false and the result cannot affect resolution.

## Reach-and-Impact

Reach [S]: PTR requests not answered by the earlier local resolver reach the classification branch.
Impact [O]: A policy-first short circuit removed one allocation and reduced time in the controlled public-proxy fixture.
Boundary: `bogus-priv` defaults to true, and the measured policy-disabled workload does not establish production frequency or router impact.

## Evidence

- [S] `proxy/proxy.go:182-196` computes `priv` unconditionally for PTR and uses it only with `p.BogusPriv`.
- [S] `proxy/util.go:126-190` parses reverse names without external side effects.
- [S] `config/config.go:183-189` defines the enabled-by-default policy.
- [O] `BenchmarkMicroPTRPolicy` composes real Proxy.Resolve calls with a local Hosts answer for `127.0.0.1` and four aliases.

## Performance-Evidence

Category: ShortCircuit.
Severity: Low for a non-default configuration with unknown traffic share.
Method, source archive, raw data, reconstruction commands, and shared limitations: [measurement note](evidence/micro-optimizations-2026-10-31.md).

- [O] Go 1.26.4, policy disabled: `988.4 -> 913.8 ns/op`.
- [O] Go 1.27.3, policy disabled: `904.4 -> 855.9 ns/op`.
- [O] Both toolchains, policy disabled: `133 -> 128 B/op` and `6 -> 5 allocs/op`.
- [O] Both disabled-policy timing comparisons were significant at `p=0.002`, with six samples per variant.
- [O] Enabled-policy timing changes were not significant (`p=0.061` and `p=0.461`); allocations remained unchanged.

The fixture has no network upstream, and local alias cardinality affects its absolute costs.
The compound prototype contains unrelated changes; isolate this branch before contribution-specific claims.

## Prior-Art

Coverage: Local finding records and broad upstream performance/allocation searches; checked=2026-10-31.
No matching root cause was found in that coverage.
Gaps: A dedicated `BogusPriv` or private-PTR upstream search, discussions, and releases remain unchecked.
Contribution fit: A small policy short circuit, conditional on demonstrated relevance of policy-disabled PTR traffic.

## Proposed-Change

Compute `priv` as `p.BogusPriv && q.Type == query.TypePTR && isPrivateReverse(q.Name)`.

## Scope-and-Constraints

- Preserve enabled-policy answers, local lookup precedence, upstream calls, discovery fallback, and error handling.
- Retain the current permissive parsing of partial or malformed reverse names instead of replacing the parser.
- Do not change the policy default or add new caching, configuration, or concurrency.
- Treat saved allocations as transient volume, not resident-memory savings.

## Verification

- Compare an isolated short circuit with policy on/off for private/public IPv4 and IPv6 PTR, partial names, and invalid names.
- Confirm byte-identical answers, errors, and resolver-call ordering at the public boundary.
- Retain ordinary A/AAAA queries as controls and measure a documented policy-disabled traffic mix.

## Publication-Blockers

An isolated representative PTR comparison, targeted prior art, work authorization, and publication target remain unresolved.

## Next-Action

Summary: Measure policy-disabled PTR traffic
Action: Compare short-circuit-only variants over a documented mix of private, public, partial, and malformed PTR requests.
Done-When: Retain unchanged policy behavior and allocation/timing results that do not depend solely on one local alias list.
