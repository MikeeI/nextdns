# ISSUE-003 — proxy: TCP admission occupies slots before request reads

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: High
Root-Cause-Confidence: High
Finding-Category: Correctness
Created: 2026-10-31
Updated: 2026-10-31
Source: `upstream/master@63564a9733bcb214d9acfde8ba31357011617f4b`

## Root-Cause

Root-Cause [S]: Each TCP read acquires a shared request slot before a request exists; failed acquisition closes the connection with responses still running.

## Reach-and-Impact

Reach [S]: TCP and UDP share the proxy admission channel.
Impact [A]: Idle connections can exhaust capacity and saturation can drop pending TCP replies.

## Evidence

- [S] `proxy/tcp.go:50-86` acquires before `readTCP` and dispatches answers asynchronously.
- [S] `proxy/proxy.go:118-146` shares the channel with UDP.
- [S] Previous review items `E3` and `E5` share this admission root cause.

## Prior-Art

Coverage: Not searched.
Gaps: Upstream issues, pull requests, discussions, and releases are unchecked.
Contribution fit: Undetermined until prior-art research.

## Proposed-Change

Admit complete requests rather than idle reads, retain a connection bound, and drain accepted replies before close.

## Scope-and-Constraints

- Preserve bounded resource use and TCP pipelining.
- Exclude upstream admission policy.

## Verification

- Exercise idle TCP connections and saturated slots with delayed replies and UDP requests.

## Publication-Blockers

Reproduction, upstream prior art, contribution direction, and publication target remain unresolved.

## Next-Action

Summary: Reproduce TCP admission failures
Action: Exercise idle connections and delayed replies at a valid low limit.
Done-When: Record client-visible results for TCP and UDP.
