# ISSUE-002 — spawn: failed window replacement leaves invalid current window

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

Review-ID: E1 (CHANGED).
Root-Cause [S]: Failed replacement removes the newly assigned current winlink without repairing `s->curw`.

## Reach-and-Impact

Reach [S]: `new-window -k` replacing the current window when pane creation fails.
Impact [S]: Later target resolution dereferences the freed winlink.
A server crash has not been reproduced.
Review severity: High.

## Evidence

- [S] `spawn.c:168-181,200-214` removes the old window, assigns the replacement, then removes it on failure.
- [S] `window.c:310-321` frees the winlink.
- [S] `cmd-find.c:1172-1175` dereferences the session's current winlink.

## Prior-Art

Local ledger checked against ISSUE-001 with no matching root cause.
External issues, pull requests, discussions, and release history remain unsearched for this finding.
Contribution target is unresolved.

## Proposed-Change

Select a surviving current window before freeing the failed replacement.
Use normal session teardown if no window remains.

## Scope-and-Constraints

`-k` permits removal of the original window.
Do not invent a transactional preservation requirement.
Cover surviving-window and empty-session recovery separately.
No source implementation or upstream publication is authorized.

## Verification

Not executed: force PTY creation failure during current-window replacement.
Check target resolution with another surviving window and with no surviving window.

## Publication-Blockers

Current upstream freshness, runtime reproduction, and external prior art remain unverified.
Work authorization, publication target, and an approved exact draft remain unset.

## Next-Action

Summary: Reproduce replacement failure
Action: Exercise failed current-window replacement on an isolated server at the recorded source revision.
Done-When: Both recovery cases have captured target-resolution results and process status.
