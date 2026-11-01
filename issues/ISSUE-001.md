# ISSUE-001 — parsing: `sscanf` numeric tokens accept trailing junk and wrap

State: Submitted
Authorized-Work: Research-and-Reporting
Publication-Target: New-issue
External-Reference: https://github.com/tmux/tmux/issues/5828
Contribution-Priority: Low
Root-Cause-Confidence: High
Finding-Category: Correctness
Created: 2026-11-01
Updated: 2026-11-01
Source: `upstream/master@a73616d781f2af13085691c741cfb0780d365328`

## Root-Cause

Root-Cause [S]: Four parsers convert numeric tokens with `sscanf` `%u`/`%d`, which accepts a numeric prefix, ignores trailing characters, and has undefined behavior on out-of-range input.
They bypass the bounded full-token `strtonum` convention used elsewhere in tmux.

## Reach-and-Impact

Reach [S]: `refresh-client -A`, `-r` and `-B` arguments, `-f pause-after=N` control flags, and `UserN` key names in `bind-key`/`unbind-key`.
Impact [O]: Malformed input is silently applied to a different target or value instead of being rejected.
Impact [S]: The affected inputs come only from the client or configuration that issues the command.
No privilege boundary is crossed and no memory-safety effect is claimed.

## Evidence

- [S] `cmd-refresh-client.c:122,157` — `sscanf(copy, "%%%u", &pane)` resolves pane IDs for `-A` and `-r`.
- [S] `monitor.c:611,615` — `sscanf(what, "%%%d", id)` and `sscanf(what, "@%d", id)` parse `-B` subscription targets.
- [S] `server-client.c:3179-3180` — `sscanf(next, "pause-after=%u", ...)` followed by unchecked `c->pause_age *= 1000`.
- [S] `key-string.c:220` — `sscanf(string, "User%u", &user)` with only an upper-bound check.
- [S] `window.c:1437-1449` — `window_pane_find_by_id_str()` already parses `%N` with `strtonum(s + 1, 0, UINT_MAX, ...)`.
- [S] `session.c:90`, `style.c:166`, `options.c:53`, `colour.c:407` — existing full-token `strtonum` usage for equivalent tokens.
- [O] `tmux -Lissue001 -f/dev/null bind-key -n User1junk display-message ok; tmux -Lissue001 list-keys` → `bind-key -T root User1 display-message ok`; environment=tmux next-3.9 at `a73616d7`, Linux x86_64.
- [O] Control client `refresh-client -A '%0junk:pause'` → `%pause %0`; same environment.
- [O] Control client `refresh-client -A '%0zz:continue'` → `%continue %0`; same environment.
- [O] Control client `refresh-client -A '%-4294967296:pause'` → `%pause %0`; same environment.
- [O] Control client `refresh-client -B 'sub:%0junk:#{pane_id}'` → `%subscription-changed sub $0 @0 0 %0 : %0`; same environment.
- [O] Control client `refresh-client -f pause-after=4294968` then `display-message -p '#{client_flags}'` → `attached,focused,control-mode,pause-after=0,UTF-8`; same environment.

## Prior-Art

Coverage: issues(open+closed) for `sscanf`, `pause-after`, `User0 user-keys bind`, `refresh-client -A pane`, `refresh-client -B subscription`; PRs(open+closed+merged) for `strtonum`; checked=2026-11-01.
Gaps: Discussions and the OpenBSD tmux mailing list were not searched.

- `https://github.com/tmux/tmux/pull/5576` — Related; closed after maintainer applied it to OpenBSD; fixed the same class (invalid numeric target offsets silently accepted) in `cmd-find.c`.
- `https://github.com/tmux/tmux/issues/3737` — Distinct; static-analyzer report on `cmd-refresh-client.c` subscription parsing, rejected as false positives.
- `https://github.com/tmux/tmux/issues/3250` — Distinct; `refresh-client -A '%0:off'` reading behavior, not token parsing.

Contribution fit: New contribution — no thread owns this root cause, and #5576 shows the maintainer accepts rejecting malformed numeric tokens.

## Proposed-Change

- `cmd-refresh-client.c:122-124,157-159`: replace `sscanf` plus `window_pane_find_by_id()` with `window_pane_find_by_id_str(copy)`.
- `monitor.c:611,615`: after the `%`/`@` prefix check, parse with `strtonum(what + 1, 0, INT_MAX, &errstr)`.
- `server-client.c:3179-3180`: match the `pause-after=` prefix, parse with `strtonum(next + 12, 0, UINT_MAX / 1000, &errstr)`, and multiply only on success.
- `key-string.c:220`: match the case-sensitive `User` prefix, then `strtonum(string + 4, 0, KEYC_NUSER, &errstr)`.

## Scope-and-Constraints

- Preserve: valid forms `%N:state`, `%*`, `@*`, `@N`, empty session target, bare `pause-after`, `User0` through `User` plus `KEYC_NUSER`, and the inclusive `KEYC_NUSER` bound.
- Preserve: existing failure routing, such as `control_remove_sub()` on invalid subscriptions and silent ignore for invalid `-A`/`-r` values.
- Exclude: `default-size` and `refresh-client -C` size parsing, hexadecimal key parsing, OSC selector parsing, and DA response parsing.
- Cost: inputs previously tolerated with trailing junk become rejected, which is an intentional observable change.

## Verification

- Reproduction commands in Evidence on a patched build → each malformed input is rejected or ignored, with no `%pause`, `%continue`, or `%subscription-changed` for `%0`, and no `User1` binding.
- Valid forms `%0:pause`, `sub:%0:#{pane_id}`, `pause-after=5`, and `User1` → behavior unchanged; `#{client_flags}` shows `pause-after=5`.

## Publication-Blockers

None.

## Next-Action

Summary: Monitor maintainer response
Action: Read https://github.com/tmux/tmux/issues/5828 and answer maintainer questions with new evidence only.
Done-When: The maintainer responds, the fix lands upstream, or the issue closes.

## Publication-Draft

Target: `tmux/tmux` new issue, template `Use this issue template`.
Title: Several numeric argument parsers accept trailing junk or wrap (refresh-client -A/-r/-B, pause-after=, UserN)

Body:

```markdown
### Issue description

A few command parsers convert numeric tokens with `sscanf("%u")`/`sscanf("%d")`, so they accept a numeric prefix followed by arbitrary characters and wrap out-of-range values. The malformed input is then silently applied to a different pane, key, or value instead of being rejected. This is the same class as #5576, which fixed silently accepted invalid offsets in `cmd-find.c`.

Affected parsers at a73616d781f2af13085691c741cfb0780d365328:

- [`cmd-refresh-client.c#L122`](https://github.com/tmux/tmux/blob/a73616d781f2af13085691c741cfb0780d365328/cmd-refresh-client.c#L122) and [`#L157`](https://github.com/tmux/tmux/blob/a73616d781f2af13085691c741cfb0780d365328/cmd-refresh-client.c#L157): `sscanf(copy, "%%%u", &pane)` for `refresh-client -A` and `-r`.
- [`monitor.c#L611-L615`](https://github.com/tmux/tmux/blob/a73616d781f2af13085691c741cfb0780d365328/monitor.c#L611-L615): `sscanf(what, "%%%d", id)` and `sscanf(what, "@%d", id)` for `refresh-client -B`.
- [`server-client.c#L3179-L3180`](https://github.com/tmux/tmux/blob/a73616d781f2af13085691c741cfb0780d365328/server-client.c#L3179-L3180): `sscanf(next, "pause-after=%u", ...)` followed by an unchecked `*= 1000`.
- [`key-string.c#L220`](https://github.com/tmux/tmux/blob/a73616d781f2af13085691c741cfb0780d365328/key-string.c#L220): `sscanf(string, "User%u", &user)`.

Steps to reproduce, with a build of current master:

    tmux -Ltest -f/dev/null new -d 'sleep 30'
    tmux -Ltest bind-key -n User1junk display-message ok
    tmux -Ltest list-keys | grep User
    # bind-key    -T root         User1                        display-message ok

Then in a control client (`tmux -Ltest -C attach`):

    refresh-client -A '%0junk:pause'          ->  %pause %0
    refresh-client -A '%0zz:continue'         ->  %continue %0
    refresh-client -A '%-4294967296:pause'    ->  %pause %0
    refresh-client -B 'sub:%0junk:#{pane_id}' ->  %subscription-changed sub $0 @0 0 %0 : %0
    refresh-client -f pause-after=4294968
    display-message -p '#{client_flags}'      ->  attached,focused,control-mode,pause-after=0,UTF-8

Expected: each malformed token is rejected or ignored, as other target and option parsers do. In the last case, 4294968 * 1000 wraps to 704 ms, so panes pause almost immediately instead of after the requested age.

A possible fix is to use the existing helpers: `window_pane_find_by_id_str()` for the two pane IDs, and `strtonum()` after the literal prefix elsewhere. The bounds would be `0..INT_MAX` for subscription IDs, `0..UINT_MAX / 1000` for `pause-after=`, and `0..KEYC_NUSER` for `UserN`. I can send a patch if that is useful.

### Required information

* tmux version: `tmux next-3.9`, built from master at a73616d7
* Platform: `Linux unknown` (x86_64, Ubuntu 24.04)
* Terminal in use: not terminal-specific; reproduced with a detached session and a control-mode client
* $TERM inside tmux: `tmux-256color`
* $TERM outside tmux: `dumb`
* Logs: not attached because the behavior is deterministic argument parsing and is fully shown by the commands above; I can attach `-vv` logs if needed.

### Disclosure

Investigated thoroughly with Claude Opus 5.5 (medium reasoning effort), using [Oh My Pi](https://github.com/can1357/oh-my-pi) as the agent framework.

This report is not generic or unreviewed AI-generated output. Its claims were checked against the cited evidence, and it includes the relevant detail intended to help maintainers resolve the issue.

If reports like this are not useful to the project, please let me know and I will refrain from submitting similar ones. My intent is to help without wasting maintainer time or energy or discouraging their work.

Thank you for your work.
```
