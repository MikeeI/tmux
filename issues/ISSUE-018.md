# ISSUE-018 — kitty: small byte limits reject valid Base64 image payloads

State: Investigating
Authorized-Work: Not-Selected
Publication-Target: Not-Selected
External-Reference: Not published.
Contribution-Priority: Low
Root-Cause-Confidence: High
Finding-Category: Correctness
Created: 2026-11-01
Updated: 2026-11-01
Source: `upstream/master@a73616d781f2af13085691c741cfb0780d365328`

## Root-Cause

Review-ID: E20 (CHANGED).
Root-Cause [S]: Image byte limits are applied to encoded length and a padded decoding upper bound.

## Reach-and-Impact

Reach [S]: Kitty uploads near small configured `image-size-limit` values.
Impact [S]: Four valid RGBA bytes encoded as `AAAA/w==` are rejected when the limit is `4`.
Ordinary default-limit transfers are not claimed to fail generally.
Review severity: Low, corrected from Medium.

## Evidence

- [S] `options-table.c:510-517` permits a minimum limit of one byte and describes uploaded or decoded image size.
- [S] `image-kitty.c:950-955` compares encoded input length with the byte limit.
- [S] `image-decode.c:49-52` checks the decoding upper bound before accounting for Base64 padding.

## Prior-Art

Local ledger checked against ISSUE-001 with no matching root cause.
External prior art and contribution target remain unresolved.

## Proposed-Change

Bound encoded input separately with overflow-safe arithmetic.
Apply the image limit to actual decoded bytes and cumulative transfer size.

## Scope-and-Constraints

Keep integer-overflow, allocation, and cumulative-size limits.
Do not merely remove size checks.
No source implementation or upstream publication is authorized.

## Verification

Not executed: accept `AAAA/w==` as a one-pixel RGBA image at limit `4`.
Reject actual decoded data exceeding the limit.

## Publication-Blockers

Current upstream freshness, protocol reproduction, and external prior art remain unverified.
Work authorization, publication target, and an approved exact draft remain unset.

## Next-Action

Summary: Reproduce exact-limit Kitty rejection
Action: Compare exact-limit and over-limit one-pixel uploads with a small explicit image limit.
Done-When: Payload lengths, configured limit, and Kitty replies are captured.
