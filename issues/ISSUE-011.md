# ISSUE-011 — session: group synchronization leaves marks on old winlinks

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

Review-ID: E10 (VALID).
Root-Cause [S]: Group synchronization replaces peer winlinks without transferring an existing mark to the replacement.

## Reach-and-Impact

Reach [S]: A mark in a peer session while another grouped session changes unrelated windows.
Impact [S]: The marked pane survives but its saved target state becomes invalid.
Review severity: Medium.

## Evidence

- [S] `session.c:705-710,733-739` creates replacement peer winlinks and frees the originals.
- [S] `session.c:774-799` demonstrates explicit mark repair in the separate renumbering path.
- [S] `cmd-find.c:694-704` rejects a marked winlink absent from the current session tree.

## Prior-Art

Local ledger checked against ISSUE-001 with no matching root cause.
ISSUE-010 concerns the independent window-swap mutation owner.
External prior art and contribution target remain unresolved.

## Proposed-Change

Map the marked old winlink to its matching replacement during synchronization.
Clear the mark only when its target actually disappears.

## Scope-and-Constraints

Preserve marks on unchanged panes.
Do not confuse stale target rejection with an observed crash.
No source implementation or upstream publication is authorized.

## Verification

Not executed: mark a pane in peer B, then create and remove unrelated windows in grouped session A.
Require `{marked}` to retain the same pane.

## Publication-Blockers

Current upstream freshness, runtime reproduction, and external prior art remain unverified.
Work authorization, publication target, and an approved exact draft remain unset.

## Next-Action

Summary: Reproduce grouped mark invalidation
Action: Resolve a peer mark across unrelated grouped-window changes.
Done-When: The pane identity and marked-target results are captured before and after synchronization.
