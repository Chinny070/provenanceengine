# Provenance Engine submission brief

**Provenance Engine** is a standalone GenLayer Intelligent Contract that preserves immutable claims, independently reproduced evidence artifacts, validator-agreed semantic findings, evidence evolution, freshness, and portable provenance receipts. It supports exact-byte `PINNED_TEXT` at a lower `TEXT_ONLY` assurance and `RENDERED_WEB` for confirmation, contradiction, and bounty eligibility.

The user submits an immutable claim and HTTPS source reference. GenLayer validators independently fetch pinned text or render a web page, derive artifact hashes and identity, and classify evidence. Deterministic contract code derives aggregate claim state from the active evidence graph; caller labels and model-authored final claim status do not control settlement.

The primitive is reusable by downstream systems without a contract-specific frontend or application:

1. **AI-agent commerce:** connect a completion claim to public work evidence; a marketplace can inspect the passport before releasing its own payment.
2. **Governance and policy monitoring:** record public policy evidence and later superseding or expiring evidence while preserving earlier state.
3. **Decentralized insurance:** preserve claim evidence and contradictory observations over time for an independent claims process to inspect.

These are example consumers, not deployed integrations. The contract does not implement multi-source publisher independence, visual/image provenance, custom challenge adjudication, or automatic deadline execution. The current implementation has local adversarial Direct Mode coverage and is deployed on Studionet at [0x4512b07d637Fe42D278B25dd778a9a1a99A38Dbb](https://explorer-studio.genlayer.com/address/0x4512b07d637Fe42D278B25dd778a9a1a99A38Dbb), with deployed-source SHA-256 `F945961C305652E41064CD119B3E015DCEBDC1F2CE17A19DB013F474D0F324B4` matching the local contract read-back. Live confirmation, contradiction, unavailable-source classification, one graph supersession lifecycle, and escrow payout/refund behavior are documented. Permission, digest-mismatch, stale-settlement, and timeout regressions are covered in Direct Mode; those specific paths have not all been exercised live.
