# Release-candidate verification

## Current pushed and deployed source

- Public commit: `d35478517283377508b44521516355c67f82f6ff` on `main`.
- Studionet contract: [0xbC3fE4d84CE9b9E57c491494De62dbD999Ca4aC2](https://explorer-studio.genlayer.com/address/0xbC3fE4d84CE9b9E57c491494De62dbD999Ca4aC2).
- Deployment transaction: [0xe15faf459e161b7ba5d7e76267dbfd33ab37261674e31d99333de5473190cdec](https://explorer-studio.genlayer.com/tx/0xe15faf459e161b7ba5d7e76267dbfd33ab37261674e31d99333de5473190cdec), `FINALIZED`, `MAJORITY_AGREE`.
- `genlayer code` read-back exactly matches the local 790-line contract after newline normalization; both SHA-256 values are `40A94B3192EBC5957620528714128D0295F4A81288E2AE8E33E1F354C41E4BEB`.
- Live rendered evidence finalized and `get_status` returned `CONFIRMED`. One initial verify transaction canceled with `NO_MAJORITY`; a retry finalized successfully.
- A separate false claim about Example Domain’s page title settled to `CONTRADICTED` after live rendered evidence and consensus verification.
- Live PINNED_TEXT submission and verification finalized; claim history records `CONSENSUS_VERIFIED: SUPPORTS`. The record-specific evidence label could not be read because the `get_evidence` RPC returned an HTML error response.
- A `0.000001 GEN` rejected-payable call finalized and emitted a refund message of the same amount to the sender. Wallet balance was observed at `0xcf9a2775fe8f87ff5` before and after; contract balance was `0x0` after settlement.
- A `0.000001 GEN` bounty finalized as `PAID`; `get_bounty` returned the evidence submitter wallet as winner. The payout receipt contains a same-value message to that wallet. A later replay attempt finalized; its validator execution receipts were errors after the bounty was already paid.
- An unavailable-source probe for a reserved nonexistent `.example` host finalized as `UNDETERMINED` with `MAJORITY_DISAGREE`; it did not establish the expected unavailable classification.

## Local result for the current source changes

- `python -m pytest -q`: 58 passed (including GenLayer Direct Mode transaction tests).
- `genvm-lint check contracts/provenance_engine.py`: 3 lint checks passed; validation passed.
- `genvm-lint schema contracts/provenance_engine.py`: 14 methods (8 views and 6 writes).
- Runtime pin: official docs' currently documented `py-genlayer` hash. The newer hash suggested by the installed linter (`5jyc…`) was tried; it fails schema loading because `allow_storage` is undefined in that candidate SDK.
- Direct Mode includes exact-byte pinned-text and independent-render hashing, class-specific assurance behavior, changed-artifact identities, forged leader decision/hash/ID/URL-field rejection, malformed/unknown/wrong-type output, prompt injection, unavailable-source handling, support/contradiction disputes, graph target and lifecycle checks, URL admission, freshness boundaries, fixed beneficiary selection, rejected-payable return behavior, replay rejection, stale refund, contradictory refund, and unresolved timeout recovery.

## Release gates

| Gate | State | Evidence / remaining work |
| --- | --- | --- |
| Contract schema and local checks | PASS | Results above are from the pushed source. |
| Substantive independent validator | PASS in Direct Mode | Live forged-leader tests require network-level validator receipts before release. |
| Render-derived hashes and identity | PASS in Direct Mode; live confirmation observed | Stored live evidence detail read-back remains incomplete. |
| Claim graph and deterministic state | PASS in Direct Mode | Live `SUPERSEDES`, `EXPIRES`, and `RESTORES` transactions remain untested. |
| Escrow beneficiary and rejected-payable refund | PARTIAL live | Submitter payout, refund message, balance observations, and replay attempt recorded; cross-wallet beneficiary isolation and all refund branches remain untested. |
| Timeout settlement and recovery | PASS in Direct Mode | No live 30-day timeout has elapsed; no time-warp is available on Studionet. |
| Challenge behavior | LIMITED | It records only an already consensus-backed conflict; no bond, appeal state machine, or independent re-adjudication. |
| Source independence | LIMITED | One support can confirm; publisher/syndication clustering is not implemented. |
| Visual provenance | REMOVED | No visual API, field, or visual claim remains. |
| Canonical Studionet deployment and source parity | PASS | Finalized deployment plus exact source read-back hash. |
| Live evidence matrix | PARTIAL | Render confirmation, contradiction, and pinned-text history are recorded. Disputed aggregate state, graph edges, a conclusive unavailable-source result, prompt injection, and challenge still need finalized live receipts. |
| Three downstream use cases | DOCUMENTED | Illustrative integration patterns, not deployed integrations. |

This is not a submission-ready declaration. The canonical contract is the 2026-09-27 deployment listed above; earlier deployments remain historical. Continue to keep each gate red or partial until its specified live evidence is recorded.
