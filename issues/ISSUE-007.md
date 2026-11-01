# ISSUE-007 — respawn: process waiter follows the replacement PID

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: High
Root-Cause-Confidence: High
Finding-Category: Correctness
Created: 2026-11-01
Updated: 2026-11-01
Source: `upstream/master@a73616d781f2af13085691c741cfb0780d365328`

## Root-Cause

Review-ID: E6 (CHANGED).
Root-Cause [S]: Forced respawn replaces `wp->pid` without separating the original `wp->wait_item`.

## Reach-and-Impact

Reach [S]: A pane started with `-W` is force-respawned before its original command is reaped.
Impact [S]: The waiter becomes associated with the replacement process's completion and status.
The ordering scenario has not been executed.
Review severity: High.

## Evidence

- [S] `tmux.1:4326-4329` promises the original shell command's completion and exit status.
- [S] `spawn.c:313-351,484` clears old process status and replaces the PID without detaching the waiter.
- [S] `server.c:508-515` resolves waiters by the pane's current PID.
- [S] `window.c:1511-1532` consumes the pane status when completing its waiter.

## Prior-Art

Local ledger checked against ISSUE-001 with no matching root cause.
External prior art and contribution target remain unresolved.

## Proposed-Change

Keep the existing waiter associated with the old PID until its actual reaping.
Do not transfer it to the replacement PID.

## Scope-and-Constraints

A fabricated immediate exit status does not preserve the documented process-wait contract.
Cover replacement spawn failure and waiter-client lifetime.
No source implementation or upstream publication is authorized.

## Verification

Not executed: keep the original process alive across respawn, then let it exit with a controlled status.
Require the waiter to report that process rather than the replacement.
Include replacement spawn failure.

## Publication-Blockers

Current upstream freshness, runtime reproduction, and external prior art remain unverified.
Work authorization, publication target, and an approved exact draft remain unset.

## Next-Action

Summary: Reproduce waiter PID transfer
Action: Trace a waiting client across forced respawn and controlled original-process exit.
Done-When: Original PID, replacement PID, completion time, and returned status are captured.
