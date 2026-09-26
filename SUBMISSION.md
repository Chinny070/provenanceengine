# Provenance Engine submission brief

**Provenance Engine** is a standalone GenLayer Intelligent Contract that preserves claims, independent rendered web evidence, validator-agreed semantic findings, evidence evolution, freshness, and portable provenance receipts.

The user submits an immutable claim and HTTPS source reference. GenLayer validators independently render the source, derive artifact hashes and identity, and classify whether it supports or contradicts the claim. Deterministic contract code derives aggregate claim state from the active evidence graph; caller labels and model-authored final claim status do not control settlement.

The primitive is reusable by downstream systems without a contract-specific frontend or application:

1. **AI-agent commerce:** connect a completion claim to public work evidence; a marketplace can inspect the passport before releasing its own payment.
2. **Governance and policy monitoring:** record public policy evidence and later superseding or expiring evidence while preserving earlier state.
3. **Decentralized insurance:** preserve claim evidence and contradictory observations over time for an independent claims process to inspect.

These are example consumers, not deployed integrations. The contract does not implement multi-source publisher independence, visual/image provenance, custom challenge adjudication, or automatic deadline execution. The current implementation has local adversarial Direct Mode coverage. The Studionet address documented in the evidence manifest belongs to an earlier source version; this source must be deployed and live-verified before the submission can claim release readiness.
