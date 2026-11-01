# ISSUE-005 — file: server-side save ignores buffered close failures

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: High
Root-Cause-Confidence: High
Finding-Category: Reliability
Created: 2026-11-01
Updated: 2026-11-01
Source: `upstream/master@a73616d781f2af13085691c741cfb0780d365328`

## Root-Cause

Review-ID: E4 (VALID).
Root-Cause [S]: The server-side stdio path ignores `fclose` failure after a successful buffered `fwrite`.

## Reach-and-Impact

Reach [S]: Attached-client or clientless `save-buffer` calls using server-side file output.
Impact [S]: A final flush failure is not propagated to the command callback.
Missing or partial saved contents have not been reproduced.
Review severity: High.

## Evidence

- [S] `file.c:321-337` selects server-side stdio and ignores the final `fclose` result.
- [S] `cmd-save-buffer.c:69-71` reports only the error passed to its callback.

## Prior-Art

Local ledger checked against ISSUE-001 with no matching root cause.
External prior art and contribution target remain unresolved.

## Proposed-Change

Capture the first `fwrite` or `fclose` error with its original `errno`.
Pass that error to the completion callback.

## Scope-and-Constraints

An ordinary unattached command uses a different client-side output path.
A successful close is not a new crash-durability or `fsync` guarantee.
No source implementation or upstream publication is authorized.

## Verification

Not executed: save a small buffer to `/dev/full` from an attached or server-side context.
Require a reported failure.

## Publication-Blockers

Current upstream freshness, runtime reproduction, and external prior art remain unverified.
Work authorization, publication target, and an approved exact draft remain unset.

## Next-Action

Summary: Reproduce buffered save failure
Action: Exercise server-side saving of a small buffer to `/dev/full` on an isolated server.
Done-When: The write failure and command-visible result are captured together.
