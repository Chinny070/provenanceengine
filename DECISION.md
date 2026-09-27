# Design decisions

- Keep Provenance Engine as a single reusable Intelligent Contract with no frontend, app layer, or consumer-specific variants.
- Offer only two evidence classes with distinct semantics: exact-byte `PINNED_TEXT` (lower assurance) and browser-rendered `RENDERED_WEB` (the only class that may confirm or contradict a claim). Keep `PINNED_JSON` and `VISUAL` out of the ABI until fully implemented and live-proven.
- Treat the caller relationship as an assertion. Independent semantic consensus decides the finding.
- Bind a canonical evidence identity to claim, URL, render/content digests, retrieval class, and normalization/schema versions; keep the caller alias separate.
- Require the validator to independently render, derive, and classify, then reject differences in typed settlement-critical fields.
- Separate claim classification from evidence-to-evidence graph classification.
- Preserve history and derive aggregate status from active, sufficiently fresh findings. Conflicting findings remain `DISPUTED`.
- Resolve payout beneficiary from the earliest active supporting evidence submitter; settlement callers cannot redirect funds.
- Reject payable bounty inputs by returning a typed sentinel and issuing a refund request, rather than assuming a revert restores attached value. Keep Studionet value-retention and transfer delivery as explicit live gates.
- Do not expose image, source-independence, automatic expiry execution, or full challenge adjudication as implemented features.

These choices favor a narrow, auditable first release over a broad ABI with unsupported evidence types.
