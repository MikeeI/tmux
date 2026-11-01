# ISSUE-017 — copy: left movement lands on wide-character padding

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

Review-ID: E18 (VALID).
Root-Cause [S]: Left movement skips padding at the starting position rather than at the destination.

## Reach-and-Impact

Reach [S]: Copy-mode navigation leftward from a narrow character following a wide character.
Impact [S]: The cursor can land on padding and skip the wide character on a later movement or selection.
Review severity: Medium.

## Evidence

- [S] `grid-reader.c:80-92` checks padding before decrementing the column.
- [S] `window-copy.c:7053-7056,7899` uses the result without padding normalization.
- [S] `window-copy.c:6187-6196` clamps only the line boundary before storing the cursor.

## Prior-Art

Local ledger checked against ISSUE-001 with no matching root cause.
External prior art and contribution target remain unresolved.

## Proposed-Change

Move left first, then skip destination padding back to the character start.

## Scope-and-Constraints

Preserve line-wrap behavior at the actual beginning of a line.
Cover multicolumn characters without treating padding as an independent character.
No source implementation or upstream publication is authorized.

## Verification

Not executed: in `界a`, move left from `a` and inspect the cursor position.
Select and copy `界` to verify the selection boundary.

## Publication-Blockers

Current upstream freshness, runtime reproduction, and external prior art remain unverified.
Work authorization, publication target, and an approved exact draft remain unset.

## Next-Action

Summary: Reproduce wide-character left movement
Action: Capture cursor columns and copied contents when navigating left through `界a`.
Done-When: The destination column and selection identify whether padding is exposed.
