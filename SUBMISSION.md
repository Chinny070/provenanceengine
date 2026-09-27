# Provenance Engine submission brief

**Provenance Engine** is a standalone GenLayer Intelligent Contract that preserves immutable claims, independently reproduced evidence artifacts, validator-agreed semantic findings, evidence evolution, freshness, and portable provenance receipts. It supports exact-byte `PINNED_TEXT` at a lower `TEXT_ONLY` assurance and `RENDERED_WEB` for confirmation, contradiction, and bounty eligibility.

The user submits an immutable claim and HTTPS source reference. GenLayer validators independently fetch pinned text or render a web page, derive artifact hashes and identity, and classify evidence. Deterministic contract code derives aggregate claim state from the active evidence graph; caller labels and model-authored final claim status do not control settlement.

The primitive is reusable by downstream systems without a contract-specific frontend or application:

1. **AI-agent commerce:** connect a completion claim to public work evidence; a marketplace can inspect the passport before releasing its own payment.
2. **Governance and policy monitoring:** record public policy evidence and later superseding or expiring evidence while preserving earlier state.
3. **Decentralized insurance:** preserve claim evidence and contradictory observations over time for an independent claims process to inspect.

These are example consumers, not deployed integrations. The contract does not implement multi-source publisher independence, visual/image provenance, custom challenge adjudication, or automatic deadline execution. The current implementation has local adversarial Direct Mode coverage and is deployed on Studionet at [0xbC3fE4d84CE9b9E57c491494De62dbD999Ca4aC2](https://explorer-studio.genlayer.com/address/0xbC3fE4d84CE9b9E57c491494De62dbD999Ca4aC2), with exact source-hash parity to the public `main` commit. Live confirmation and escrow flows are documented. The remaining live verification matrix is incomplete, so this brief does not claim submission readiness.
