# ISSUE-008 — capture: hyperlink collection stops at the pane width

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

Review-ID: E7 (CHANGED).
Root-Cause [S]: Capture-wide hyperlink deduplication has capacity `gd->sx`, regardless of the number of captured lines.

## Reach-and-Impact

Reach [S]: `capture-pane -H` over more than one pane-width of distinct hyperlinks.
Impact [S]: Further unique links are silently omitted.
Review severity: Medium, corrected from High.

## Evidence

- [S] `cmd-capture-pane.c:243-245` stops adding links at `gd->sx`.
- [S] `cmd-capture-pane.c:348-355` shares the same list and count across all captured lines.
- [S] `tmux.1:2970-2973` describes capturing hyperlinks from the specified lines.

## Prior-Art

Local ledger checked against ISSUE-001 with no matching root cause.
External prior art and contribution target remain unresolved.

## Proposed-Change

Grow the capture-wide link-ID list as needed.
Preserve the existing deduplication policy.

## Scope-and-Constraints

Repeated-link output is not proven to require per-line duplication.
Resetting the list per line would change semantics beyond the demonstrated capacity defect.
No source implementation or upstream publication is authorized.

## Verification

Not executed: capture more than `gd->sx` unique links across multiple lines.
Require every unique link to be present.

## Publication-Blockers

Current upstream freshness, runtime reproduction, and external prior art remain unverified.
Work authorization, publication target, and an approved exact draft remain unset.

## Next-Action

Summary: Reproduce hyperlink capture truncation
Action: Capture a deterministic multiline set exceeding the pane-width unique-link count.
Done-When: Expected and captured unique-link sets have been compared.
