# ISSUE-016 — redraw: visual style equality ignores underline colour

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

Review-ID: E17 (VALID).
Root-Cause [S]: `grid_cells_look_equal` omits underline colour `us` from visible-style equality.

## Reach-and-Impact

Reach [S]: Adjacent cells with different underline colours on a terminal supporting that attribute.
Impact [S]: Redraw groups them into one run emitted with a single style.
The visual effect has not been observed in a live terminal.
Review severity: Medium.

## Evidence

- [S] `grid.c:266-278` compares visible attributes without `us`.
- [S] `tty-draw.c:302-311,331-343` groups cells using that predicate before emitting one style.
- [S] `tty.c:2511-2517` independently handles underline colour.

## Prior-Art

Local ledger checked against ISSUE-001 with no matching root cause.
External prior art and contribution target remain unresolved.

## Proposed-Change

Include `us` in the visible-style comparison.

## Scope-and-Constraints

Check consumers of the shared equality predicate.
Do not alter unrelated attribute defaults or rendering capabilities.
No source implementation or upstream publication is authorized.

## Verification

Not executed: emit adjacent differently coloured underlines and compare before and after a full redraw.
Use a terminal that supports underline colour.

## Publication-Blockers

Current upstream freshness, terminal reproduction, and external prior art remain unverified.
Work authorization, publication target, and an approved exact draft remain unset.

## Next-Action

Summary: Reproduce underline redraw mismatch
Action: Capture rendering of adjacent underline colours across a full pane redraw.
Done-When: Terminal capability and before/after colour results are documented.
