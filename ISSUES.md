# Issue and Pull Request Tracking

Read this index at the start of every agent session before repository work.
`FORMAT.md` owns research, lifecycle, drafting, implementation, and publication rules.
Each linked `issues/ISSUE-NNN.md` is the complete authoritative record for one root cause.
This file owns `Next finding ID` and projects current issue-file state.
`Next-Action` is the 2–6 word `Next-Action/Summary` projection from the issue record.
When a row disagrees with its issue file, correct the row from the issue file in the same task.

Next finding ID: ISSUE-048

## Open-Findings

| ID | Finding | State | Authorized-Work | Publication-Target | Contribution-Priority | Next-Action | External-Reference |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [ISSUE-001](issues/ISSUE-001.md) | config: destructive saves ignore write failures | Investigating | Not-Selected | Not-Selected | High | Recreate config save reproducer | Not published. |
| [ISSUE-002](issues/ISSUE-002.md) | OpenWrt: forwarder recovery depends on process memory | Investigating | Not-Selected | Not-Selected | High | Reproduce forwarder recovery failure | Not published. |
| [ISSUE-003](issues/ISSUE-003.md) | proxy: TCP admission closes connections with pending replies | Investigating | Not-Selected | Not-Selected | High | Recreate TCP admission reproducer | Not published. |
| [ISSUE-004](issues/ISSUE-004.md) | proxy: concurrent TCP responses interleave DNS frames | Investigating | Not-Selected | Not-Selected | High | Recreate TCP framing reproducer | Not published. |
| [ISSUE-005](issues/ISSUE-005.md) | proxy: listener registration races shutdown | Investigating | Not-Selected | Not-Selected | High | Recreate listener shutdown reproducer | Not published. |
| [ISSUE-006](issues/ISSUE-006.md) | cache: query semantics omitted from cache identity | Investigating | Not-Selected | Not-Selected | High | Recreate DNS flag collision | Not published. |
| [ISSUE-007](issues/ISSUE-007.md) | cache: DNS53 upstreams share an empty namespace | Investigating | Not-Selected | Not-Selected | High | Reproduce upstream cache collision | Not published. |
| [ISSUE-008](issues/ISSUE-008.md) | resolver: stale cache fallback overwrites aliased query | Investigating | Not-Selected | Not-Selected | High | Recreate alias buffer reproducer | Not published. |
| [ISSUE-009](issues/ISSUE-009.md) | resolver: DNS53 forwarding cannot complete truncated replies | Investigating | Not-Selected | Not-Selected | High | Recreate DNS53 TCP reproducer | Not published. |
| [ISSUE-010](issues/ISSUE-010.md) | proxy: UDP truncation cuts DNS messages at arbitrary bytes | Investigating | Not-Selected | Not-Selected | High | Reproduce malformed truncated replies | Not published. |
| [ISSUE-011](issues/ISSUE-011.md) | router: configure errors do not prevent setup | Investigating | Not-Selected | Not-Selected | High | Reproduce rejected router setup | Not published. |
| [ISSUE-012](issues/ISSUE-012.md) | router: failed startup leaves prestart changes applied | Investigating | Not-Selected | Not-Selected | High | Reproduce router startup rollback | Not published. |
| [ISSUE-013](issues/ISSUE-013.md) | OpenWrt: explicit port 53 does not enable port zero | Investigating | Not-Selected | Not-Selected | High | Reproduce explicit dnsmasq port | Not published. |
| [ISSUE-014](issues/ISSUE-014.md) | OpenWrt: restore removes DHCP options it did not add | Investigating | Not-Selected | Not-Selected | High | Reproduce DHCP option deletion | Not published. |
| [ISSUE-015](issues/ISSUE-015.md) | Merlin: active NextDNS skips existing postconf script | Investigating | Not-Selected | Not-Selected | High | Reproduce skipped postconf script | Not published. |
| [ISSUE-016](issues/ISSUE-016.md) | profiles: interface condition is not represented durably | Investigating | Not-Selected | Not-Selected | High | Reproduce addressless interface rule | Not published. |
| [ISSUE-017](issues/ISSUE-017.md) | control: startup unlinks an active Unix socket | Investigating | Not-Selected | Not-Selected | High | Reproduce control socket takeover | Not published. |
| [ISSUE-018](issues/ISSUE-018.md) | control: stop leaves accept loop and clients running | Investigating | Not-Selected | Not-Selected | High | Reproduce retained control clients | Not published. |
| [ISSUE-019](issues/ISSUE-019.md) | discovery: failed semaphore acquisition releases another owner | Investigating | Not-Selected | Not-Selected | High | Recreate discovery ownership reproducer | Not published. |
| [ISSUE-020](issues/ISSUE-020.md) | discovery: hosts refresh mutates state under read lock | Investigating | Not-Selected | Not-Selected | High | Reproduce hosts refresh race | Not published. |
| [ISSUE-021](issues/ISSUE-021.md) | Windows: DNS reset discards interface errors | Investigating | Not-Selected | Not-Selected | High | Reproduce Windows reset errors | Not published. |
| [ISSUE-022](issues/ISSUE-022.md) | host: failed resolver swap does not restore backup | Investigating | Not-Selected | Not-Selected | High | Reproduce resolver swap failure | Not published. |
| [ISSUE-023](issues/ISSUE-023.md) | service: install removes the old service before validation | Investigating | Not-Selected | Not-Selected | High | Reproduce service replacement failure | Not published. |
| [ISSUE-024](issues/ISSUE-024.md) | activation: persisted mode ignores operation and save failures | Investigating | Not-Selected | Not-Selected | High | Reproduce activation state divergence | Not published. |
| [ISSUE-025](issues/ISSUE-025.md) | service: subprocess output is read before copy completion | Investigating | Not-Selected | Not-Selected | High | Reproduce service output race | Not published. |
| [ISSUE-026](issues/ISSUE-026.md) | forwarder: DNS domain matching is case sensitive | Investigating | Not-Selected | Not-Selected | Medium | Recreate forwarder case reproducer | Not published. |
| [ISSUE-027](issues/ISSUE-027.md) | discovery: transient DNS errors become negative cache entries | Investigating | Not-Selected | Not-Selected | Medium | Reproduce discovery negative caching | Not published. |
| [ISSUE-028](issues/ISSUE-028.md) | hosts: localhost fallback uses an unnormalized key | Investigating | Not-Selected | Not-Selected | Medium | Reproduce localhost fallback failure | Not published. |
| [ISSUE-029](issues/ISSUE-029.md) | netstatus: address changes lack historical snapshots | Investigating | Not-Selected | Not-Selected | Medium | Reproduce missed address change | Not published. |
| [ISSUE-030](issues/ISSUE-030.md) | endpoint: DoH discovery reads response body only once | Investigating | Not-Selected | Not-Selected | Medium | Reproduce fragmented discovery reply | Not published. |
| [ISSUE-031](issues/ISSUE-031.md) | endpoint: DoH URLs lose their configured port | Investigating | Not-Selected | Not-Selected | Medium | Reproduce nondefault DoH port | Not published. |
| [ISSUE-032](issues/ISSUE-032.md) | Linux: gateway fallback parses route fields incorrectly | Investigating | Not-Selected | Not-Selected | Medium | Reproduce gateway route parsing | Not published. |
| [ISSUE-033](issues/ISSUE-033.md) | forwarder: configured maximum TTL is not propagated | Investigating | Not-Selected | Not-Selected | Medium | Reproduce forwarded TTL mismatch | Not published. |
| [ISSUE-034](issues/ISSUE-034.md) | service: listener failure can be reported as startup success | Investigating | Not-Selected | Not-Selected | High | Reproduce lost listener error | Not published. |
| [ISSUE-035](issues/ISSUE-035.md) | mDNS: cached addresses ignore TTL and goodbye records | Investigating | Not-Selected | Not-Selected | Medium | Reproduce stale mDNS records | Not published. |
| [ISSUE-036](issues/ISSUE-036.md) | service: startup retry ignores cancellation | Investigating | Not-Selected | Not-Selected | Medium | Reproduce startup cancellation delay | Not published. |
| [ISSUE-037](issues/ISSUE-037.md) | forwarder: DoH URL query parameters are not preserved | Investigating | Not-Selected | Not-Selected | Medium | Reproduce DoH query loss | Not published. |
| [ISSUE-038](issues/ISSUE-038.md) | resolver: client discovery runs before fresh cache hits | Investigating | Not-Selected | Not-Selected | High | Recreate cache-hit discovery reproducer | Not published. |
| [ISSUE-039](issues/ISSUE-039.md) | reporting: reverse lookup precedes overriding MAC name | Investigating | Not-Selected | Not-Selected | High | Reproduce discarded reverse lookup | Not published. |
| [ISSUE-040](issues/ISSUE-040.md) | control: late replies satisfy later same-name requests | Investigating | Not-Selected | Not-Selected | Medium | Recreate control correlation reproducer | Not published. |
| [ISSUE-041](issues/ISSUE-041.md) | control: send deadlines omit lock and socket writes | Investigating | Not-Selected | Not-Selected | Medium | Recreate control cancellation reproducer | Not published. |
| [ISSUE-042](issues/ISSUE-042.md) | OpenWrt: DHCP DNS membership accepts address substrings | Investigating | Not-Selected | Not-Selected | Medium | Reproduce DHCP substring match | Not published. |
| [ISSUE-043](issues/ISSUE-043.md) | reporting: peer IP is formatted twice per callback | Investigating | Not-Selected | Not-Selected | Low | Measure complete reporting workload | Not published. |
| [ISSUE-044](issues/ISSUE-044.md) | proxy: hosts misses allocate a discarded error | Investigating | Not-Selected | Not-Selected | Low | Measure realistic hosts misses | Not published. |
| [ISSUE-045](issues/ISSUE-045.md) | proxy: UDP ancillary buffer is allocated per receive | Investigating | Not-Selected | Not-Selected | Low | Validate UDP ancillary buffer ownership | Not published. |
| [ISSUE-046](issues/ISSUE-046.md) | proxy: TCP frame prefixes allocate a temporary buffer | Investigating | Not-Selected | Not-Selected | Low | Verify TCP prefix error semantics | Not published. |
| [ISSUE-047](issues/ISSUE-047.md) | proxy: private PTR classification runs with policy disabled | Investigating | Not-Selected | Not-Selected | Low | Measure policy-disabled PTR traffic | Not published. |

## Archived-Findings

| ID | Finding | Authorized-Work | Publication-Target | Contribution-Priority | Archive-Reason | External-Reference |
| --- | --- | --- | --- | --- | --- | --- |
