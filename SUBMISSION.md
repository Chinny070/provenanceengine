# Provenance Engine — submission text

## Suggested form fields

**Title:** Provenance Engine — on-chain evidence provenance and bounty escrow

**Notes / Description:**

Provenance Engine is a standalone GenLayer Intelligent Contract that records claims and retrieved evidence, then derives claim status from validator consensus. On Studionet, a caller-supplied `CONTRADICTS` label did not override a consensus `SUPPORTS` result; a pinned-digest mismatch produced `INTEGRITY_MISMATCH` and `UNAVAILABLE`; an evidence-free challenge reverted without changing status or history; and a different wallet settling a bounty paid the evidence submitter. Live tests also returned `UNAVAILABLE` for an unreachable source and completed a `SUPERSEDES` graph transition. Adversarial Direct Mode tests cover these paths, hash mismatch, challenge/reward authorization, stale settlement, and timeout recovery. Deployed source read-back matches the repository. The production seven-day stale refund is staged, not live-verified; a separate 60-second timeout probe does not replace the canonical 30-day timeout.

## Evidence links to attach

1. **Canonical Studionet contract:** [0x4512b07d637Fe42D278B25dd778a9a1a99A38Dbb](https://explorer-studio.genlayer.com/address/0x4512b07d637Fe42D278B25dd778a9a1a99A38Dbb)
2. **Deployment and live transaction evidence:** [DEPLOYMENT_EVIDENCE.md](https://github.com/Chinny070/provenanceengine/blob/main/docs/DEPLOYMENT_EVIDENCE.md)
3. **Repository and source:** [Chinny070/provenanceengine](https://github.com/Chinny070/provenanceengine)
4. **Adversarial test and release-gate results:** [RELEASE_CANDIDATE_VERIFICATION.md](https://github.com/Chinny070/provenanceengine/blob/main/docs/RELEASE_CANDIDATE_VERIFICATION.md)

The deployment evidence document links each live transaction directly and labels the separate short-timeout deployment and pending production stale test. The canonical deployment transaction is [0x809920a2feac4b4ce40d58dc27f9fde594d2eb485686446c6e306e3e0c395f97](https://explorer-studio.genlayer.com/tx/0x809920a2feac4b4ce40d58dc27f9fde594d2eb485686446c6e306e3e0c395f97). Its source read-back matches SHA-256 `F945961C305652E41064CD119B3E015DCEBDC1F2CE17A19DB013F474D0F324B4`.

## Scope and verification limits

The production deployment has live evidence for relationship/decision conflict, retrieved-byte/pinned-hash mismatch, rejection of an evidence-free challenge, cross-wallet bounty beneficiary isolation, unavailable-source consensus, and one complete `SUPERSEDES` lifecycle. Local verification includes 65 pytest tests, 65 GenLayer Direct Mode tests, three GenVM lint checks, and a 14-method schema check. The production stale-evidence refund awaits the seven-day freshness boundary (`2026-10-04 19:08:04 UTC`); the full 30-day timeout has not elapsed on the canonical deployment. Prompt injection, `EXPIRES`, `RESTORES`, and a valid evidence-backed challenge remain without live verification. See the linked release-gate document for the full matrix.

These are example integration patterns, not deployed integrations. The contract does not establish publisher independence or authenticity, provide visual/image provenance, implement appeal adjudication, or execute deadlines automatically.
