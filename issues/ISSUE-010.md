# ISSUE-010 — swap: marked pane state is only partially repaired

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: Medium
Root-Cause-Confidence: High
Finding-Category: Correctness
Created: 2026-11-01
Updated: 2026-11-01
Source: `upstream/master@a73616d781f2af13085691c741cfb0780d365328`

## Root-Cause

Review-ID: E9 (VALID).
Root-Cause [S]: Window swapping repairs only a source-side marked winlink and omits its session association.

## Reach-and-Impact

Reach [S]: A marked destination pane or a marked source pane swapped across sessions.
Impact [S]: Marked-target validation rejects the resulting inconsistent state.
Review severity: Medium.

## Evidence

- [S] `cmd-swap-window.c:76-82` swaps both window associations but updates only `marked_pane.wl == wl_src`.
- [S] `cmd-find.c:694-704` requires the winlink to belong to the marked session and window.

## Prior-Art

Local ledger checked against ISSUE-001 with no matching root cause.
ISSUE-011 concerns a separate group-synchronization mutation owner.
External prior art and contribution target remain unresolved.

## Proposed-Change

Rebuild the complete marked state for either affected side, including its session.

## Scope-and-Constraints

Preserve the marked pane's identity rather than its former slot.
Cover same-session and cross-session swaps.
No source implementation or upstream publication is authorized.

## Verification

Not executed: mark each side independently and resolve `{marked}` after both swap variants.

## Publication-Blockers

Current upstream freshness, runtime reproduction, and external prior art remain unverified.
Work authorization, publication target, and an approved exact draft remain unset.

## Next-Action

Summary: Reproduce swapped mark invalidation
Action: Run the source-mark and destination-mark swap matrix on isolated sessions.
Done-When: Marked-target resolution is recorded for same-session and cross-session swaps.
