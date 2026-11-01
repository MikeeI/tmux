# ISSUE-012 — buffer: unnamed append ignores the automatic top buffer

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

Review-ID: E11 (VALID).
Root-Cause [S]: `set-buffer -a` only resolves an existing buffer when `-b` supplies a name.

## Reach-and-Impact

Reach [S]: Appending without `-b` while an automatic buffer exists.
Impact [S]: The command creates a new buffer containing only the new text instead of the combined text.
Review severity: Medium.

## Evidence

- [S] `cmd-set-buffer.c:67-70,118-128` leaves `pb` unset without `-b` and skips copying existing data.
- [S] `paste.c:289-291` creates a new automatic buffer for a null name.
- [S] `tmux.1:8655-8656,8874-8876` defines the default buffer and append behavior.

## Prior-Art

Local ledger checked against ISSUE-001 with no matching root cause.
ISSUE-006 concerns automatic status after copy-mode replacement rather than missing default-buffer lookup.
External prior art and contribution target remain unresolved.

## Proposed-Change

Resolve the top automatic buffer for `-a` without `-b`.
Update it while retaining its automatic status.

## Scope-and-Constraints

Preserve named append behavior and the no-existing-buffer case.
No source implementation or upstream publication is authorized.

## Verification

Not executed: run `set-buffer old`, `set-buffer -a new`, then `show-buffer`.
Require `oldnew`.
Also cover append without an existing buffer.

## Publication-Blockers

Current upstream freshness, runtime reproduction, and external prior art remain unverified.
Work authorization, publication target, and an approved exact draft remain unset.

## Next-Action

Summary: Reproduce unnamed append behavior
Action: Compare named and unnamed append with and without a preexisting automatic buffer.
Done-When: Returned contents and automatic-buffer lookup are recorded for each case.
