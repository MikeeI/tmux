# ISSUE-013 — monitor: single-pane subscription skips retained dead panes

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

Review-ID: E13 (VALID).
Root-Cause [S]: Single-pane subscription evaluation returns early when the pane's PTY descriptor is closed.

## Reach-and-Impact

Reach [S]: Control subscriptions and hook monitors targeting a concrete retained pane.
Impact [S]: `pane_dead` cannot transition to true through this evaluation path.
The all-panes path does not apply the same descriptor guard.
Review severity: Medium.

## Evidence

- [S] `monitor.c:251-253` excludes `wp->fd == -1`.
- [S] `format.c:2315-2317` requires `fd == -1` and a ready exit status for `pane_dead = 1`.
- [S] `tmux.1:1540-1563` describes format-change subscriptions for specific panes or all panes.

## Prior-Art

ISSUE-001 concerns numeric subscription-target parsing, not evaluation after process exit.
No matching local root cause was found.
External prior art and contribution target remain unresolved.

## Proposed-Change

Evaluate existing panes independently of whether their PTY is open.

## Scope-and-Constraints

Retain the nonexistent-pane check.
Use `remain-on-exit` so the target still exists after process completion.
No source implementation or upstream publication is authorized.

## Verification

Not executed: subscribe to `#{pane_dead}` for a specific pane with `remain-on-exit`, then end its process.
Compare the result with an all-panes subscription.

## Publication-Blockers

Current upstream freshness, runtime reproduction, and external prior art remain unverified.
Work authorization, publication target, and an approved exact draft remain unset.

## Next-Action

Summary: Reproduce missing dead-pane notification
Action: Capture concrete-pane and all-pane subscription results across retained-pane process exit.
Done-When: The `pane_dead` transition is compared between both subscription modes.
