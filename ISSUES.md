# Issue and Pull Request Tracking

Read this index at the start of every agent session before repository work.
`FORMAT.md` owns research, lifecycle, drafting, implementation, and publication rules.
Each linked `issues/ISSUE-NNN.md` is the complete authoritative record for one root cause.
This file owns `Next finding ID` and projects current issue-file state.
`Next-Action` is the 2–6 word `Next-Action/Summary` projection from the issue record.
When a row disagrees with its issue file, correct the row from the issue file in the same task.

Next finding ID: ISSUE-020

## Open-Findings

| ID | Finding | State | Authorized-Work | Publication-Target | Contribution-Priority | Next-Action | External-Reference |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [ISSUE-001](issues/ISSUE-001.md) | parsing: `sscanf` numeric tokens accept trailing junk and wrap | Submitted | Research-and-Reporting | New-issue | Low | Monitor maintainer response | https://github.com/tmux/tmux/issues/5828 |
| [ISSUE-002](issues/ISSUE-002.md) | spawn: failed window replacement leaves invalid current window | Investigating | Not-Selected | Not-Selected | High | Reproduce replacement failure | Not published. |
| [ISSUE-003](issues/ISSUE-003.md) | pipe: input-only pipe receives output with no reader | Investigating | Not-Selected | Not-Selected | High | Measure input-only pipe behavior | Not published. |
| [ISSUE-004](issues/ISSUE-004.md) | paste: disabled pane input still permits buffer deletion | Investigating | Not-Selected | Not-Selected | High | Reproduce disabled-input deletion | Not published. |
| [ISSUE-005](issues/ISSUE-005.md) | file: server-side save ignores buffered close failures | Investigating | Not-Selected | Not-Selected | High | Reproduce buffered save failure | Not published. |
| [ISSUE-006](issues/ISSUE-006.md) | copy: append converts an automatic buffer into a named buffer | Investigating | Not-Selected | Not-Selected | Medium | Reproduce automatic buffer conversion | Not published. |
| [ISSUE-007](issues/ISSUE-007.md) | respawn: process waiter follows the replacement PID | Investigating | Not-Selected | Not-Selected | High | Reproduce waiter PID transfer | Not published. |
| [ISSUE-008](issues/ISSUE-008.md) | capture: hyperlink collection stops at the pane width | Investigating | Not-Selected | Not-Selected | Medium | Reproduce hyperlink capture truncation | Not published. |
| [ISSUE-009](issues/ISSUE-009.md) | link: grouped-session rejection follows index mutation | Investigating | Not-Selected | Not-Selected | Medium | Reproduce rejected link mutation | Not published. |
| [ISSUE-010](issues/ISSUE-010.md) | swap: marked pane state is only partially repaired | Investigating | Not-Selected | Not-Selected | Medium | Reproduce swapped mark invalidation | Not published. |
| [ISSUE-011](issues/ISSUE-011.md) | session: group synchronization leaves marks on old winlinks | Investigating | Not-Selected | Not-Selected | Medium | Reproduce grouped mark invalidation | Not published. |
| [ISSUE-012](issues/ISSUE-012.md) | buffer: unnamed append ignores the automatic top buffer | Investigating | Not-Selected | Not-Selected | Medium | Reproduce unnamed append behavior | Not published. |
| [ISSUE-013](issues/ISSUE-013.md) | monitor: single-pane subscription skips retained dead panes | Investigating | Not-Selected | Not-Selected | Medium | Reproduce missing dead-pane notification | Not published. |
| [ISSUE-014](issues/ISSUE-014.md) | control: unlink reports closure to an unaffected session | Investigating | Not-Selected | Not-Selected | Medium | Reproduce cross-session close notification | Not published. |
| [ISSUE-015](issues/ISSUE-015.md) | resize: floating edge direction leaks between flags | Investigating | Not-Selected | Not-Selected | Medium | Reproduce combined resize direction | Not published. |
| [ISSUE-016](issues/ISSUE-016.md) | redraw: visual style equality ignores underline colour | Investigating | Not-Selected | Not-Selected | Medium | Reproduce underline redraw mismatch | Not published. |
| [ISSUE-017](issues/ISSUE-017.md) | copy: left movement lands on wide-character padding | Investigating | Not-Selected | Not-Selected | Medium | Reproduce wide-character left movement | Not published. |
| [ISSUE-018](issues/ISSUE-018.md) | kitty: small byte limits reject valid Base64 image payloads | Investigating | Not-Selected | Not-Selected | Low | Reproduce exact-limit Kitty rejection | Not published. |
| [ISSUE-019](issues/ISSUE-019.md) | copy: history collection shifts a non-following refresh anchor | Investigating | Not-Selected | Not-Selected | Medium | Reproduce history refresh jump | Not published. |

## Archived-Findings

| ID | Finding | Authorized-Work | Publication-Target | Contribution-Priority | Archive-Reason | External-Reference |
| --- | --- | --- | --- | --- | --- | --- |
