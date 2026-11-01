# ISSUE-014 — control: unlink reports closure to an unaffected session

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: Medium
Root-Cause-Confidence: Medium
Finding-Category: API
Created: 2026-11-01
Updated: 2026-11-01
Source: `upstream/master@a73616d781f2af13085691c741cfb0780d365328`

## Root-Cause

Review-ID: E14 (CHANGED).
Root-Cause [S]: The unlink callback ignores the event session when selecting clients for `%window-close`.

## Reach-and-Impact

Reach [S]: The same window is linked to two sessions and only one session removes its link.
Impact [S]: A control client in the unaffected session receives a close message despite retaining its link.
Actual consumer state loss has not been observed.
Review severity: Medium.

## Evidence

- [S] `events.c:193-197` includes the event's session and window in its payload.
- [S] `session.c:355-358` fires the unlink event before removing the originating link.
- [S] `control-notify.c:130-149` broadcasts close messages according to each client's current window lookup.

## Prior-Art

Local ledger checked against ISSUE-001 with no matching root cause.
External protocol discussions and contribution target remain unresolved.

## Proposed-Change

Tie `%window-close` to the removed session association.
Do not tell clients with unchanged associations to close their window.

## Scope-and-Constraints

Distinguish session unlink from actual global window destruction.
Do not suppress legitimate destruction notifications through a blanket session filter.
No source implementation or upstream publication is authorized.

## Verification

Not executed: link a window into two sessions and capture both control streams during one unlink.
Compare with actual window destruction.

## Publication-Blockers

Current upstream freshness, control-protocol reproduction, and external prior art remain unverified.
The complete notification compatibility matrix still needs review.
Work authorization, publication target, and an approved exact draft remain unset.

## Next-Action

Summary: Reproduce cross-session close notification
Action: Capture both session control streams across a single unlink and actual destruction.
Done-When: Notifications are matched to the surviving and removed session associations.
