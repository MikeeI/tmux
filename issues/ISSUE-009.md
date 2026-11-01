# ISSUE-009 — link: grouped-session rejection follows index mutation

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

Review-ID: E8 (VALID).
Root-Cause [S]: Window-index shuffling precedes the rejection of links between sessions in the same group.

## Reach-and-Impact

Reach [S]: `link-window -a` or `-b` between grouped sessions with destination indices to shift.
Impact [S]: A rejected operation leaves destination indices changed without group synchronization.
Review severity: Medium.

## Evidence

- [S] `cmd-move-window.c:95-108` calls `winlink_shuffle_up` before checking `server_link_window` failure.
- [S] `server-fn.c:296-300` rejects different sessions in the same group.
- [S] `window.c:2552-2577` mutates destination indices during shuffling.

## Prior-Art

Local ledger checked against ISSUE-001 with no matching root cause.
External prior art and contribution target remain unresolved.

## Proposed-Change

Check group incompatibility before `winlink_shuffle_up` mutates indices.

## Scope-and-Constraints

Preserve accepted link and move ordering.
The reported defect is the grouped-session rejection path, not every possible shuffle failure.
No source implementation or upstream publication is authorized.

## Verification

Not executed: record both grouped sessions' index mappings before a rejected `link-window -a`.
Require both mappings to remain unchanged afterward.

## Publication-Blockers

Current upstream freshness, runtime reproduction, and external prior art remain unverified.
Work authorization, publication target, and an approved exact draft remain unset.

## Next-Action

Summary: Reproduce rejected link mutation
Action: Attempt insertion between grouped sessions with occupied consecutive indices.
Done-When: Before and after mappings establish whether rejection mutates either session.
