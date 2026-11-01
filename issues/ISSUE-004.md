# ISSUE-004 — paste: disabled pane input still permits buffer deletion

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: High
Root-Cause-Confidence: High
Finding-Category: Correctness
Created: 2026-11-01
Updated: 2026-11-01
Source: `upstream/master@a73616d781f2af13085691c741cfb0780d365328`

## Root-Cause

Review-ID: E3 (VALID).
Root-Cause [S]: `paste-buffer -d` deletes its buffer even when `PANE_INPUTOFF` prevents every input write.

## Reach-and-Impact

Reach [S]: Pasting with deletion into a pane disabled through `select-pane -d`.
Impact [S]: The buffer is removed without delivering its contents.
The scenario has not been executed.
Review severity: High.

## Evidence

- [S] `cmd-paste-buffer.c:88-127` excludes disabled input from the complete write block.
- [S] `cmd-paste-buffer.c:129-132` deletes the buffer independently and returns success.

## Prior-Art

Local ledger checked against ISSUE-001 with no matching root cause.
External prior art and contribution target remain unresolved.

## Proposed-Change

Report disabled input before deleting the buffer.

## Scope-and-Constraints

Preserve deletion after an accepted paste.
Do not claim that queue acceptance guarantees application consumption.
No source implementation or upstream publication is authorized.

## Verification

Not executed: disable input with `select-pane -d`, then run `paste-buffer -d` with a named buffer.
Require an error and retained contents.

## Publication-Blockers

Current upstream freshness, runtime reproduction, and external prior art remain unverified.
Work authorization, publication target, and an approved exact draft remain unset.

## Next-Action

Summary: Reproduce disabled-input deletion
Action: Attempt paste-with-deletion into an isolated disabled pane.
Done-When: Command status, delivered input, and remaining buffer contents are captured.
