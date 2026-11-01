# ISSUE-019 — copy: history collection shifts a non-following refresh anchor

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: Medium
Root-Cause-Confidence: High
Finding-Category: UI
Created: 2026-11-01
Updated: 2026-11-01
Source: `upstream/master@a73616d781f2af13085691c741cfb0780d365328`

## Root-Cause

Review-ID: A1 (ADD).
Root-Cause [S]: Refresh reuses an absolute visible-line index after history collection shifts retained lines.

## Reach-and-Impact

Reach [S]: Non-following copy-mode refresh after history collection with unchanged grid generation.
Impact [S]: The displayed anchor advances even when the formerly anchored line remains available.
The live UI scenario has not been executed.
Review severity: Medium.

## Evidence

- [S] `window-copy.c:3300-3332` promises to retain visible lines but reuses the old absolute top index.
- [S] `window-copy.c:531-557` moves retained lines by `collected` during incremental synchronization.
- [S] `grid.c:425-428` removes oldest history lines and increments `scroll_collected`.
- [O] In-memory arithmetic with old history `100`, top `50`, collected `10`, and added `10` yields actual top `50`.
  The same retained line moves to index `40`.
  Environment: Python arithmetic check during the 2026-11-01 review, not a tmux runtime reproduction.

## Prior-Art

Local ledger checked against ISSUE-001 with no matching root cause.
External prior art and contribution target remain unresolved.

## Proposed-Change

Subtract collected history lines from the prior visible anchor before recalculating the view offset.
Apply this mapping only while grid generation remains compatible.
Clamp when the anchored line itself has been removed.

## Scope-and-Constraints

Preserve follow-mode behavior.
Do not infer stable line identity across an incompatible grid generation or reflow.
No source implementation or upstream publication is authorized.

## Verification

Not executed: show a retained history line away from the following position and trigger history collection.
Require the same retained line at the same display position after refresh.

## Publication-Blockers

Current upstream freshness, UI reproduction, and external prior art remain unverified.
Work authorization, publication target, and an approved exact draft remain unset.

## Next-Action

Summary: Reproduce history refresh jump
Action: Track a retained visible line through same-generation history collection and non-following refresh.
Done-When: Before and after line identities and viewport positions are captured.
