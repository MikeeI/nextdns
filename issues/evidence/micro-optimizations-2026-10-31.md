# Micro-optimization measurements: 2026-10-31

This note owns shared measurement provenance for ISSUE-043 through ISSUE-047.
Each issue record owns its root cause, contribution assessment, and next action.
The experiment changed temporary copies, not the fork's production source.

## Retained evidence

Archive: [micro-optimizations-2026-10-31.tar.gz](micro-optimizations-2026-10-31.tar.gz).
SHA-256: `e40613098ddb558363283407fbe4ee5c236fa749bf51d00007f07ff314923773`.

- `source.tar` contains canonical `upstream/master@63564a9733bcb214d9acfde8ba31357011617f4b`.
- Five `micro_bench_test.go` files contain the benchmark fixtures and temporary semantic checks.
- `candidate/` contains six modified source files for the final compound experimental variant.
- `before126.txt`, `after126.txt`, `before127.txt`, and `after127.txt` contain the final raw measurements.
- `summary126.txt` and `summary127.txt` contain the corresponding `benchstat` comparisons.
- `environment.json` records compiler flags and runtime selection settings.
- `screen-before126.txt` and `screen-after126.txt` retain earlier exploratory measurements, not the final variant.

The final compound variant includes a rejected simple-question parser fast path alongside the five recorded candidates.
Do not apply the whole candidate tree as an implementation or attribute its combined latency to one change.
The source for every earlier screening variant is not retained; only the final compound variant is reconstructable.
The original temporary directories and binaries were removed after archiving.

## Environment and method

Source was checked against the live canonical master reference before measurement.
Both toolchains were verified on generated binaries: Go 1.26.4 and Go 1.27.3.
The host was Linux/amd64 on an AMD Ryzen 9 5950X, with `GOAMD64=v1`, `CGO_ENABLED=1`, and empty `GOFLAGS` and `GOEXPERIMENT`.
Each toolchain used serial A/B/B/A blocks, three samples per block, yielding six samples per variant.
Benchmark processes were pinned to CPU 2 with one benchmark execution thread and 300-ms sample targets.
No other agent-owned builds or tests ran alongside the timing blocks.
External host load and CPU frequency were not controlled.
Comparisons use medians and `benchstat` significance, not best-round results or unrelated-workload geomeans.

## Workload boundaries

- `BenchmarkMicroClientInfo` invokes the real reporting callback with an empty discovery resolver, no MAC, and no discovery I/O.
- `BenchmarkMicroHostsMiss` invokes `hostsResolve` with the repository's `discovery.Dummy` source to isolate the discarded error.
- `BenchmarkMicroReadTCP` uses a resettable memory reader and existing destination buffer; it excludes socket latency.
- `BenchmarkMicroUDP` uses real IPv4 and IPv6 loopback sockets, local Hosts answers, and the production discovery adapter.
- `BenchmarkMicroPTRPolicy` composes an outer proxy with an inner local-Hosts proxy, not a network upstream.
- Local Hosts workloads depend on `/etc/hosts`; the measured reverse lookup of `127.0.0.1` returned four aliases.
- The original `/etc/hosts` is not included, so different local aliases can change PTR costs and answer sizes.
- Allocation figures describe transient allocation volume, not resident-memory savings or a production query rate.
- Router latency, throughput, GC pressure, and loaded latency distributions remain unmeasured.

The compound variant's UDP latency differences were not significant with either toolchain.
The Go-1.26 512-byte TCP timing comparison was inconclusive despite eliminating its temporary allocation.

## Reconstruct the final experiment

Run these GNU tar/coreutils commands from the repository root into a new temporary directory:

```sh
work=$(mktemp -d /tmp/nextdns-micro.XXXXXX)
tar -xzf issues/evidence/micro-optimizations-2026-10-31.tar.gz -C "$work"
tar -xf "$work/source.tar" -C "$work"
tar --skip-old-files -xf "$work/source.tar" -C "$work/candidate"
cd "$work"
cp --parents micro_bench_test.go discovery/micro_bench_test.go resolver/micro_bench_test.go resolver/query/micro_bench_test.go proxy/micro_bench_test.go candidate/
```

The baseline command used for the final comparison was:

```sh
taskset -c 2 env GOTOOLCHAIN=go1.26.4 go test -mod=readonly -p=1 -run '^$' -bench '^BenchmarkMicro(ClientInfo|ReadTCP|HostsMiss|HostsHit|PTRPolicy|UDP|Query)$' -benchmem -benchtime=300ms -count=3 -cpu=1 . ./resolver/query ./proxy
```

For the candidate, use `go -C candidate test` with the same arguments.
Run baseline, candidate, candidate, baseline serially and retain each variant's stdout separately.
Repeat with `GOTOOLCHAIN=go1.27.3`, then run `benchstat` on each toolchain's before/after files.
Use an available CPU affinity on another host and record that departure from the original environment.
Isolate a selected change before making contribution-specific performance claims.

## Executed semantic and build checks

- [O] `TestMicroIDFingerprint` matched across variants: `9337b9b8b9eb5267`.
- [O] `TestMicroNormalizationFingerprint` matched: `5abf9c1e95194a79`.
- [O] `TestMicroQueryFingerprint` matched: `4f70d539108a651a`.
- [O] `TestMicroTCPFingerprint` matched: `284829b501d2da10`.
- [O] `TestMicroUDPSmoke` passed with real IPv4 and IPv6 responses.
- [O] Existing query-package tests passed for the final experimental variant under Go 1.26.4.
- [O] Targeted root, query, and proxy semantic checks passed under Go 1.27.3.
- [O] `GOTOOLCHAIN=go1.27.3 go -C candidate test -mod=readonly -race -run '^TestMicroUDPSmoke$' ./proxy` passed.
- [O] Go-1.26.4 test binaries cross-compiled for Linux/ARMv7, Linux/MIPSLE softfloat, and Windows/amd64.

The cross-compilation targets covered the root, proxy, and query packages with `CGO_ENABLED=0`.
Cross-compilation is not target-platform execution, and the Linux race smoke does not prove all OOB lifetime cases.
The five temporary semantic tests remain only inside the research archive; no maintained test suite was changed.

## Rejected or unproven directions

- The host-normalization helper improved in isolation, but the tested lowercase public lookups already allocated nothing.
- A larger `shortID` stack buffer did not save allocations for normal MAC/IP sizes and regressed other input sizes.
- A simple-question parser guard improved plain queries but regressed EDNS/ECS cases with both toolchains.
- A deferred OPT-offset walk did not justify its added complexity and showed an ECS/IPv6 regression in screening.
- A fixed small Hosts-list reserve helped an alias-dependent workload, not a demonstrated general workload.
- Existing cache-key hashing and cached-question validation measured zero allocations in the examined Go-1.26 baselines.

These directions do not receive new finding IDs from this experiment.
