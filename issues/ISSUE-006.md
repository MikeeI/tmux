# ISSUE-006 — copy: append converts an automatic buffer into a named buffer

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

Review-ID: E5 (CHANGED).
Root-Cause [S]: Copy-mode append replaces the automatic top buffer through the explicitly named setter.

## Reach-and-Impact

Reach [S]: `append-selection` when an automatic buffer exists.
Impact [S]: Default paste lookup skips the resulting buffer.
Its contents remain accessible by name.
Automatic buffer-limit behavior also changes.
Review severity: Medium, corrected from High.

## Evidence

- [S] `window-copy.c:6787-6796` retrieves the top buffer name and calls `paste_set` with it.
- [S] `paste.c:313` sets `automatic = 0` for named replacement.
- [S] `paste.c:119-125` skips nonautomatic buffers during top lookup.
- [S] `paste.c:329-335` provides status-preserving `paste_replace`.

## Prior-Art

Local ledger checked against ISSUE-001 with no matching root cause.
External prior art and contribution target remain unresolved.

## Proposed-Change

Use `paste_replace` for an existing automatic buffer.
Create a new automatic buffer only when none exists.

## Scope-and-Constraints

Preserve buffer identity and automatic status.
This is loss of default access, not complete data loss.
No source implementation or upstream publication is authorized.

## Verification

Not executed: append repeatedly, paste without a buffer name, and exercise `buffer-limit`.
Require the combined contents to remain automatically accessible.

## Publication-Blockers

Current upstream freshness, runtime reproduction, and external prior art remain unverified.
Work authorization, publication target, and an approved exact draft remain unset.

## Next-Action

Summary: Reproduce automatic buffer conversion
Action: Exercise copy-mode append with a single automatic buffer on an isolated server.
Done-When: Named and default lookup results establish the post-append buffer status.
