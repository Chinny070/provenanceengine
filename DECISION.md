# Design decisions

- Keep Provenance Engine as a single reusable Intelligent Contract with no frontend, app layer, or consumer-specific variants.
- Offer only `RENDER_HTML`; remove caller-provided retrieval classes and artifact hashes instead of pretending those classes have working semantics.
- Treat the caller relationship as an assertion. Independent semantic consensus decides the finding.
- Bind a canonical evidence identity to claim, URL, render/content digests, retrieval class, and normalization/schema versions; keep the caller alias separate.
- Require the validator to independently render, derive, and classify, then reject differences in typed settlement-critical fields.
- Separate claim classification from evidence-to-evidence graph classification.
- Preserve history and derive aggregate status from active, sufficiently fresh findings. Conflicting findings remain `DISPUTED`.
- Resolve payout beneficiary from the earliest active supporting evidence submitter; settlement callers cannot redirect funds.
- Do not expose image, source-independence, automatic expiry execution, or full challenge adjudication as implemented features.

These choices favor a narrow, auditable first release over a broad ABI with unsupported evidence types.
