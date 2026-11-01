# ISSUE-003 — pipe: input-only pipe receives output with no reader

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: High
Root-Cause-Confidence: High
Finding-Category: Reliability
Created: 2026-11-01
Updated: 2026-11-01
Source: `upstream/master@a73616d781f2af13085691c741cfb0780d365328`

## Root-Cause

Review-ID: E2 (CHANGED).
Root-Cause [S]: Pane output is queued for `pipe-pane -I` despite the child not reading that socket direction.

## Reach-and-Impact

Reach [S]: Input-only pipes whose commands keep the socket open without reading from it.
Impact [S]: Output continues to accumulate after the socket stops accepting writes.
`window_pane_destroy_ready` also waits for the queued output to drain.
Memory growth and delayed pane removal have not been measured in tmux.
Review severity: High.

## Evidence

- [S] `cmd-pipe-pane.c:145-155` connects input-only child stdin to `/dev/null` rather than the socket.
- [S] `window.c:1709-1714` queues pane output whenever a pipe exists.
- [S] `window.c:498-503` prevents destruction while the pipe output queue is nonempty.
- [O] A direct ctypes call to installed `libevent-2.1.so.7` returned `enabled = 4` after `bufferevent_new`.
  Environment: Linux x86_64, review session on 2026-11-01.
  Value `4` is `EV_WRITE`.

## Prior-Art

Local ledger checked against ISSUE-001 with no matching root cause.
External prior art and contribution target remain unresolved.

## Proposed-Change

Store the configured pipe output direction explicitly.
Queue pane output only when that direction is enabled.

## Scope-and-Constraints

The previous claim that libevent writing starts disabled was disproven.
Disabling writes alone would retain the erroneous enqueueing.
Preserve `-O` and `-IO` behavior.
No source implementation or upstream publication is authorized.

## Verification

Not executed: use `-I 'sleep 10000'` and produce more output than the socket can buffer.
Compare memory use and pane teardown with `-O`.

## Publication-Blockers

Current upstream freshness, tmux reproduction, and external prior art remain unverified.
Work authorization, publication target, and an approved exact draft remain unset.

## Next-Action

Summary: Measure input-only pipe behavior
Action: Exercise sustained output and pane exit with an isolated input-only pipe.
Done-When: Queue growth and pane teardown are measured against an output-enabled control.
