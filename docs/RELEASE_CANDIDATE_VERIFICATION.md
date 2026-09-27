# Release-candidate verification

## Local result for the current source changes

- `python -m pytest -q`: 58 passed (including GenLayer Direct Mode transaction tests).
- `genvm-lint check contracts/provenance_engine.py`: 3 lint checks passed; validation passed.
- `genvm-lint schema contracts/provenance_engine.py`: 14 methods (8 views and 6 writes).
- Runtime pin: official docs' currently documented `py-genlayer` hash. The newer hash suggested by the installed linter (`5jyc…`) was tried; it fails schema loading because `allow_storage` is undefined in that candidate SDK.
- Direct Mode includes exact-byte pinned-text and independent-render hashing, class-specific assurance behavior, changed-artifact identities, forged leader decision/hash/ID/URL-field rejection, malformed/unknown/wrong-type output, prompt injection, unavailable-source handling, support/contradiction disputes, graph target and lifecycle checks, URL admission, freshness boundaries, fixed beneficiary selection, rejected-payable return behavior, replay rejection, stale refund, contradictory refund, and unresolved timeout recovery.

## Release gates

| Gate | State | Evidence / remaining work |
| --- | --- | --- |
| Contract schema and local checks | PASS locally | Results above; re-run against the final pushed commit. |
| Substantive independent validator | PASS in Direct Mode | Live forged-leader tests require network-level validator receipts before release. |
| Render-derived hashes and identity | PASS in Direct Mode | Live render proof against canonical deployment is still required. |
| Claim graph and deterministic state | PASS in Direct Mode | Live supersede/expire/restore transactions are still required. |
| Escrow beneficiary, replay, timeout | PASS in Direct Mode | Live deposit, correct-beneficiary payout, rejected-payable balance/refund, transfer delivery, replay, and recovery receipts are still required. |
| Challenge behavior | LIMITED | It records only an already consensus-backed conflict; no bond, appeal state machine, or independent re-adjudication. |
| Source independence | LIMITED | One support can confirm; publisher/syndication clustering is not implemented. |
| Visual provenance | REMOVED | No visual API, field, or visual claim remains. |
| Canonical Studionet deployment and source parity | NOT YET | The last deployed address uses a different source hash; deploy the final pushed commit and verify parity. |
| Live evidence matrix | NOT YET | Pin text, render, confirmation, contradiction, dispute, all graph edges, unavailable source, prompt-injection fixture, and challenge need finalized receipts. |
| Three downstream use cases | DOCUMENTED | Illustrative integration patterns, not deployed integrations. |

This is not a submission-ready declaration. The hardening deployment at `0x2827dB51F877691e0001De9Fc8A29e3Ba1Ea69CA` predates this addendum and a live CLI-argument fix; it is not the release candidate. Preserve its receipts as historical evidence. Do not mark gates green until the final pushed source is deployed and the live matrix, including escrow balance changes, is measured.
