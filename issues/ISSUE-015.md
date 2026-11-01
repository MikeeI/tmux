# ISSUE-015 — resize: floating edge direction leaks between flags

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

Review-ID: E15 (VALID).
Root-Cause [S]: `opposite` is initialized outside the direction loop and never reset after `-U` or `-L`.

## Reach-and-Impact

Reach [S]: Floating-pane resize commands combining opposite-edge and normal-edge directions.
Impact [S]: Later `-D` or `-R` operations move the wrong origin edge.
Review severity: Medium.

## Evidence

- [S] `cmd-resize-pane.c:66-68,145-173` evaluates directions in order and only assigns `opposite = 1`.
- [S] `layout.c:956-973` shifts the top or left offset when `opposite` is set.

## Prior-Art

Local ledger checked against ISSUE-001 with no matching root cause.
External prior art and contribution target remain unresolved.

## Proposed-Change

Derive `opposite` independently for every direction processed.

## Scope-and-Constraints

Preserve tiled-pane behavior and existing directional flag order.
No source implementation or upstream publication is authorized.

## Verification

Not executed: combine `-U` and `-R` on a floating pane.
Compare resulting top and right edges with independent expected adjustments.

## Publication-Blockers

Current upstream freshness, runtime reproduction, and external prior art remain unverified.
Work authorization, publication target, and an approved exact draft remain unset.

## Next-Action

Summary: Reproduce combined resize direction
Action: Record floating-pane geometry before and after combined upper-edge and right-edge changes.
Done-When: Actual offsets and dimensions are compared with the intended two-edge adjustment.
