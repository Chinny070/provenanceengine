# Release-candidate verification

## Current pushed and deployed source

- Public repository: [Chinny070/provenanceengine](https://github.com/Chinny070/provenanceengine), branch `main`.
- Studionet contract: [0x4512b07d637Fe42D278B25dd778a9a1a99A38Dbb](https://explorer-studio.genlayer.com/address/0x4512b07d637Fe42D278B25dd778a9a1a99A38Dbb).
- Deployment transaction: [0x809920a2feac4b4ce40d58dc27f9fde594d2eb485686446c6e306e3e0c395f97](https://explorer-studio.genlayer.com/tx/0x809920a2feac4b4ce40d58dc27f9fde594d2eb485686446c6e306e3e0c395f97), `FINALIZED`, `MAJORITY_AGREE`.
- `genlayer code` read-back matches the local 829-line contract after newline normalization; local SHA-256 is `F945961C305652E41064CD119B3E015DCEBDC1F2CE17A19DB013F474D0F324B4`.
- Live rendered evidence finalized and `get_status` returned `CONFIRMED`. One initial verify transaction canceled with `NO_MAJORITY`; a retry finalized successfully.
- A separate false claim about Example Domain’s page title settled to `CONTRADICTED` after live rendered evidence and consensus verification.
- Live PINNED_TEXT submission and verification finalized; claim history records `CONSENSUS_VERIFIED: SUPPORTS`. The record-specific evidence label could not be read because the `get_evidence` RPC returned an HTML error response.
- A `0.000001 GEN` rejected-payable call finalized and emitted a refund message of the same amount to the sender. Wallet balance was observed at `0xcf9a2775fe8f87ff5` before and after; contract balance was `0x0` after settlement.
- A `0.000001 GEN` bounty finalized as `PAID`; `get_bounty` returned the evidence submitter wallet as winner. The payout receipt contains a same-value message to that wallet. A later replay attempt finalized; its validator execution receipts were errors after the bounty was already paid.
- The regression probe for an unavailable reserved `.example` host finalized, and a live `get_status` call returned `UNAVAILABLE`; see the new deployment evidence section for its transactions.
- A live same-host `SUPERSEDES` relation finalized with an active edge. The claim moved from `CONFIRMED` to `CONTRADICTED`; the probe used an `httpbin.org` response reflecting a query-supplied record, so it verifies contract graph execution rather than publisher authority.
- The active deployment is `0x4512b07d637Fe42D278B25dd778a9a1a99A38Dbb`; its deployed source read-back matches SHA-256 `F945961C305652E41064CD119B3E015DCEBDC1F2CE17A19DB013F474D0F324B4`. The deployment manifest now points to this address and transaction.

## Local result for the current source changes

- `python -m pytest -q`: 65 passed (including GenLayer Direct Mode transaction tests).
- `gltest tests --network localnet`: 65 passed.
- `genvm-lint check contracts/provenance_engine.py`: 3 lint checks passed; validation passed.
- `genvm-lint schema contracts/provenance_engine.py`: 14 methods (8 views and 6 writes).
- Runtime pin: official docs' currently documented `py-genlayer` hash. The newer hash suggested by the installed linter (`5jyc…`) was tried; it fails schema loading because `allow_storage` is undefined in that candidate SDK.
- Direct Mode includes exact-byte pinned-text and independent-render hashing, distinct observation/artifact IDs and alias-collision resistance, class-specific assurance behavior, changed-artifact identities, forged leader output rejection, malformed/unknown/wrong-type output, prompt injection, the full unavailable validator path, support/contradiction disputes, graph authority continuity and pinned-text rejection, graph target/history checks, graph-capacity isolation, oversized artifact fail-closed behavior, duplicate challenge rejection, URL admission, freshness boundaries, fixed beneficiary selection, rejected-payable return behavior, replay rejection, stale refund, contradictory refund, and unresolved timeout recovery.
- Steward-requested adversarial regressions are explicit: relationship/decision conflicts (`test_consensus_finding_not_submitter_assertion`), evidence-free attacker challenge reversion with unchanged challenge count/history (`test_arbitrary_challenge_cannot_clear_confirmation`), rejected early reward calls and sponsor-only timeout refund (`test_unresolved_bounty_refunds_after_fixed_timeout`), stale settlement refund with no winning evidence (`test_stale_claim_and_settlement_both_refund`), fetched-byte versus pinned-digest mismatch (`test_pinned_text_digest_mismatch_is_inconclusive_not_contradiction`), and attacker-triggered settlement paying the evidence submitter (`test_front_runner_cannot_take_bounty_from_evidence_submitter`). These remain Direct Mode results; they are not live-network tests.

## Release gates

| Gate | State | Evidence / remaining work |
| --- | --- | --- |
| Contract schema and local checks | PASS | Results above are from the pushed source. |
| Substantive independent validator | PASS in Direct Mode | Live forged-leader tests require network-level validator receipts before release. |
| Render-derived hashes and identity | PASS in Direct Mode; live confirmation observed | Stored live evidence detail read-back remains incomplete. |
| Claim graph and deterministic state | PASS in Direct Mode | Live `SUPERSEDES`, `EXPIRES`, and `RESTORES` transactions remain untested. |
| Escrow beneficiary and rejected-payable refund | PASS in Direct Mode; PARTIAL live | A third-party settlement caller cannot select the payout address; qualifying evidence submitter is the fixed beneficiary. Live payout/replay and invalid-payable refund are recorded. Stale/timeout refund branches are Direct Mode only. |
| Timeout settlement and recovery | PASS in Direct Mode | No live 30-day timeout has elapsed; no time-warp is available on Studionet. |
| Challenge behavior | PASS for evidence-backed admission in Direct Mode | Challenges are permissionless, but require a finalized opposite finding; unsupported/evidence-free calls revert without changing count or history. No bond, appeal state machine, or independent re-adjudication is implemented. |
| Source independence | LIMITED | One support can confirm; publisher/syndication clustering is not implemented. |
| Visual provenance | REMOVED | No visual API, field, or visual claim remains. |
| Canonical Studionet deployment and source parity | PASS | Deployment and newline-normalized source parity recorded above. |
| Unavailable-source consensus path | PASS live | Finalized verification and `get_status == UNAVAILABLE`. |
| Claim graph and deterministic state | PARTIAL live | One finalized `SUPERSEDES` lifecycle observed; live `EXPIRES` and `RESTORES` remain untested. The test source reflected a query-supplied record. |
| Live evidence matrix | PARTIAL | Render confirmation, contradiction, pinned-text history, unavailable classification, and one graph supersession are recorded. Prompt injection, challenge, mismatch, stale settlement, and timeout still lack finalized live receipts; timeout cannot be advanced on Studionet. |
| Three downstream use cases | DOCUMENTED | Illustrative integration patterns, not deployed integrations. |

This is not a submission-ready declaration. The canonical contract is the 2026-09-27 deployment listed above; earlier deployments remain historical. Continue to keep each gate red or partial until its specified live evidence is recorded.
