# project-tmux-fork

<essential-rule>
AGENTS.md is the sole authoritative project context file.
Read and edit AGENTS.md directly.
</essential-rule>

## Development Rules

Before launching agents, apply skill-xray, skill-expert, and skill-brutal to the task.
Surface expert-level issues, non-obvious issues, blindspots, stale assumptions, and hidden dependencies.
Also surface missed constraints, edge cases, false positives, verification gaps, overclaims, and weak assumptions.
Identify improvement potential, inefficiencies, and what is wrong without softening.
Use these findings to design safe slices, sequencing, checks, and boundaries for complete agent results.

Every agent prompt must require skill-xray, skill-expert, and skill-brutal for the assigned scope before acting.
It must surface non-obvious issues, blindspots, stale assumptions, hidden dependencies, and edge cases.
It must also surface verification gaps, overclaims, failure modes, weak assumptions, and what is wrong.
The agent must adjust its approach, challenge its assumptions, and flag misleading or incomplete output risks.

Implementation assignments must cover existing patterns, callers, exported-symbol consumers, and failure modes.
They must also cover concurrency safety and lifecycle cleanup.
Each assignment must state `Test decision: none` or `Test decision: update`.
`update` must name the exact existing test that follows an intentional contract change.
Never request new tests.
Prohibit broad edits, unrelated cleanup, and unassigned files.

No vague agents.
Each assignment needs exact targets, non-goals, evidence anchors, acceptance criteria, and an output contract.

Commit completed units continuously.
Before each commit, use skill-git-commit-format to determine whether staged effects are one coherent unit.
The skill owns commit-message format and evidence.
After the boundary is valid, run the repository-owned commit and push workflow.
Do not commit every trivial edit immediately or defer unrelated work into one end-of-session commit.

Every project-level quality command is quiet by default and verbose on demand.
This policy applies regardless of language or toolchain.
It covers Make targets, package scripts, Python CLIs, shell quality gates, and test runners.
Successful checks print only compact status such as `format: ok`, `lint: ok`, `test: ok`, or `check: ok`.
On failure, exit non-zero and print the failing step, exit code, and enough output to act without rerunning.
Full raw output must remain available through `--verbose`, `VERBOSE=1`, or the underlying tool's verbose mode.
New quality commands and future language setup must follow this policy instead of inventing another logging contract.

# Repository Guidelines

## Project Overview

tmux is a C terminal multiplexer with a persistent server and detachable clients.
The upstream project supports multiple Unix-like platforms.

## Fork & Upstream Contribution Intent

- Official upstream: [tmux/tmux](https://github.com/tmux/tmux).
- This checkout is the [MikeeI/tmux](https://github.com/MikeeI/tmux) fork, not an independent product.
- On 2026-11-01, a sample of 100 closed upstream pull requests contained 8 merged and 84 unmerged fork-originated requests.
- These counts are an indicator rather than proof of upstream policy.
- Given that sample, propose `Research-and-Reporting` only unless the user selects `Pull-Request-Implementation`.
- The goal is evidence-backed upstream issues, comments, and pull requests.
- Apply `skill-fork-contribution-tracking` for tracking and upstream handoff.
- Apply `skill-maintainer-communication` before external contributions.
- Apply `skill-semantic-compression-3` to tracking content and `skill-git-commit-format` to commits.
- Search existing work first and follow current upstream templates and disclosure rules.
- Never choose `Authorized-Work` or `Publication-Target` on the user's behalf.
- `Research-and-Reporting` permits research, issues, and comments but no source implementation.
- `Pull-Request-Implementation` authorizes only the scope recorded for the finding.
- Reproduce claimed bugs against current upstream and run the narrowest conclusive verification.
- Keep fork-only context and personal commits out of upstream contribution diffs.

## Branch Roles

- The canonical upstream base is `master`.
- The `personal` branch owns fork context and contribution tracking.
- Use separate `feat/issue-NNN`, `fix/issue-NNN`, or `chore/issue-NNN` branches based on `upstream/master` for contributions.
- `origin` is the personal fork over SSH and `upstream` is canonical tmux.
- Never push `personal` to upstream or open a pull request from it.

## Finding and Contribution Ledger

- Read `ISSUES.md` at the start of every agent session before repository work.
- `ISSUES.md` owns the finding ID allocator and compact overview.
- `issues/ISSUE-NNN.md` owns the complete record for one root cause.
- `FORMAT.md` owns the evidence, lifecycle, implementation, and publication contracts.
- Search the index and relevant records before allocating a permanent ID.
- Create the record, index row, and next allocator value together.
- Update the record and index together whenever projected state changes.
- New findings start as `Investigating` with work and publication target `Not-Selected`.
- The user selects the authorized work and publication target for each finding.
- Run the validator bundled with `skill-fork-contribution-tracking` after ledger mutations.
- Keep `AGENTS.md`, `FORMAT.md`, `ISSUES.md`, and `issues/` out of upstream contribution diffs.

### External publication approval

Show the exact current draft and target before publishing externally.
Publish only after the user approves that exact draft and target.
A changed draft or target requires renewed approval.

## Architecture & Data Flow

- `tmux.c`, `client.c`, `server.c`, `server-client.c`, and `proc.c` own process and client-server machinery.
- `cmd-*.c`, `cmd-parse.y`, and `cmd-queue.c` implement command parsing and execution.
- `session.c`, `window*.c`, `screen*.c`, `grid*.c`, and `tty*.c` manage sessions, windows, rendering, and terminals.
- `osdep-*.c` and `compat/` provide platform-specific and compatibility implementations.
- Shared declarations and protocol definitions live in `tmux.h` and `tmux-protocol.h`.

## Development Commands

- Build a Git checkout with `sh autogen.sh && ./configure && make`.
- Build dependencies include a C compiler, make, pkg-config, yacc or bison, autoconf, automake, libevent, and ncurses.
- Read `README` and `configure.ac` for platform and build requirements.
- Run a separate server with `tmux -Ltest -f/dev/null new` when investigating configuration-independent behavior.
- Do not kill or change an existing server without first checking the target and impact.

## Code Conventions

- Follow the surrounding C code and upstream contribution guidance.
- Preserve platform-specific behavior and shared protocol compatibility when modifying common interfaces.

## Important Files

- `README` documents dependencies, build instructions, and upstream support.
- `.github/CONTRIBUTING.md` documents issue reproduction and required diagnostic information.
- `.github/ISSUE_TEMPLATE/` contains upstream issue forms.
- `tmux.1` is the manual and `CHANGES` tracks project changes.
- `Makefile.am` and `configure.ac` own the Autotools build.

## Testing & QA

- `regress/` contains upstream regression scripts.
- Select checks matching the affected behavior and inspect their prerequisites before running them.
- Upstream asks reporters to reproduce with the latest release or Git master and check the manual and existing issues.
