# Provenance Engine — submission text

## Suggested form fields

**Title:** Provenance Engine — on-chain evidence provenance and bounty escrow

**Notes / Description:**

Provenance Engine is a standalone GenLayer Intelligent Contract that records claims and independently verified evidence. Its hardened design removes shared lifetime caps: creators have 64 claims; each claim has 128 evidence and graph slots; outside callers share at most 32 evidence slots, with 16 per address, preserving at least 96 slots for the creator; and each sponsor has eight open bounties per claim. Direct Mode tests prove caller isolation and exercise status, passport, and settlement at the maximum 128 evidence and graph records. The hardened source is deployed at the linked Studionet address with matching source read-back. Live tests verified a claim, evidence, `CONFIRMED` status and passport, and a 1-wei payout to its evidence submitter. A fresh production stale-evidence refund is staged until `2026-10-04 20:47:37 UTC`. Address-scoped quotas are not sybil-resistant.

## Evidence links to attach

1. **Canonical hardened Studionet contract:** [0xD745F2093De04c4C3c9a4648981D5b6d880163d0](https://explorer-studio.genlayer.com/address/0xD745F2093De04c4C3c9a4648981D5b6d880163d0)
2. **Deployment and live transaction evidence:** [DEPLOYMENT_EVIDENCE.md](https://github.com/Chinny070/provenanceengine/blob/main/docs/DEPLOYMENT_EVIDENCE.md)
3. **Repository and source:** [Chinny070/provenanceengine](https://github.com/Chinny070/provenanceengine)
4. **Adversarial tests and release gates:** [RELEASE_CANDIDATE_VERIFICATION.md](https://github.com/Chinny070/provenanceengine/blob/main/docs/RELEASE_CANDIDATE_VERIFICATION.md)

The canonical deployment transaction is [0xfaff041e78eb4e25b574010a3694e8c35fa8b9d22a871a5a93eb6f14218b4617](https://explorer-studio.genlayer.com/tx/0xfaff041e78eb4e25b574010a3694e8c35fa8b9d22a871a5a93eb6f14218b4617). The normalized deployed-source SHA-256 is `ED6D31F4668D5E6BB9494E5F1266F79157F968AAF904FD1A1597188A70083E41`.

## Verification limits

The production stale refund is pending the seven-day freshness boundary. The canonical timeout remains 30 days and has not elapsed live. Earlier live regressions, including unavailable-source classification and graph supersession, were run on the superseded `0x4512…` deployment; they are historical evidence, not live verification of this hardened deployment. Prompt injection and live 128-item worst-case execution remain unverified; maximum-size status/passport/settlement was exercised in Direct Mode. Quotas are address-scoped and can be bypassed by users controlling multiple wallets. See the linked verification document for the full matrix.
