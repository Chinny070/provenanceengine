# Release-candidate verification

## Current hardened deployment

- Public repository: [Chinny070/provenanceengine](https://github.com/Chinny070/provenanceengine), branch `main`.
- Studionet contract: [0xD745F2093De04c4C3c9a4648981D5b6d880163d0](https://explorer-studio.genlayer.com/address/0xD745F2093De04c4C3c9a4648981D5b6d880163d0).
- Deployment: [0xfaff041e78eb4e25b574010a3694e8c35fa8b9d22a871a5a93eb6f14218b4617](https://explorer-studio.genlayer.com/tx/0xfaff041e78eb4e25b574010a3694e8c35fa8b9d22a871a5a93eb6f14218b4617), finalized `MAJORITY_AGREE`.
- Deployed `genlayer code` read-back matches the 889-line source after LF newline normalization. SHA-256: `ED6D31F4668D5E6BB9494E5F1266F79157F968AAF904FD1A1597188A70083E41`.

## Capacity hardening

The contract no longer has global lifetime caps for claims, evidence, history, graph edges, or bounties. Admission limits are scoped: 64 claims per creator address; 128 evidence and graph edges per claim; at most 32 external evidence submissions per claim and 16 from any one external address, leaving 96 evidence slots reserved for the claim creator; 260 history entries per claim; and eight open bounties per sponsor/claim pair. An attacker can consume only their own creator quota and the small external-evidence allowance on a target claim. Their bounty allowance cannot consume another sponsor's allowance.

Direct Mode regressions exercise former global capacities as already full, create claims beyond the old 256-claim limit, verify that a new claimant can still submit evidence, and confirm that one submitter cannot consume the creator's reserved evidence slots. A separate test fills the per-claim evidence and graph bounds and successfully runs status, passport, and bounty settlement. The test for one sponsor's open-bounty limit confirms another sponsor remains able to create a bounty. These quotas are address-scoped and do not prevent a user with multiple wallets from obtaining additional quotas.

Scoped TreeMap indexes remove scans over unrelated claims, evidence, history, and graph edges. Status/freshness/settlement inspect at most 128 evidence records; passports inspect at most 128 evidence and 128 edge records; histories return at most 260 entries. The worst-case execution test is Direct Mode; it does not measure target-network gas or prove that arbitrary total deployment storage can grow without platform limits.

## Current-source live smoke test

On the hardened deployment, claim `pe-hardening-live-20260927` was created in [0xc64291a2802d7ab43a798c586e48674217e53a1165dcf588d0b240719c4491cf](https://explorer-studio.genlayer.com/tx/0xc64291a2802d7ab43a798c586e48674217e53a1165dcf588d0b240719c4491cf), its rendered evidence was submitted in [0x5b55647c01bb3701367a1ed92f0aa3d25579825711af0554e424ed368cc8be1c](https://explorer-studio.genlayer.com/tx/0x5b55647c01bb3701367a1ed92f0aa3d25579825711af0554e424ed368cc8be1c), and consensus verification finalized in [0xeeeaae91995c76e11aa5b5e71490766f14e4dc8b66ef3500abf7fad92dbe295b](https://explorer-studio.genlayer.com/tx/0xeeeaae91995c76e11aa5b5e71490766f14e4dc8b66ef3500abf7fad92dbe295b). Live `get_status` and the passport both returned `CONFIRMED`; `get_history` returned the expected three claim/evidence events.

A 1-wei bounty was created in [0x4b38a753ff12bbb434742f34f0e5f99b0d68d402aecfcd07f48e37ae30244a99](https://explorer-studio.genlayer.com/tx/0x4b38a753ff12bbb434742f34f0e5f99b0d68d402aecfcd07f48e37ae30244a99) and paid in [0x1c857f6da31a0fc51a9a6656bdebb416f79a74546f03472ca7e0b8445bcaa288](https://explorer-studio.genlayer.com/tx/0x1c857f6da31a0fc51a9a6656bdebb416f79a74546f03472ca7e0b8445bcaa288). The receipt contains a 1-wei transfer to the evidence submitter; live `get_bounty` returned `PAID`, amount `0`, and that submitter as winner.

## Remaining live gates

A fresh production stale-evidence case is staged on this exact deployment. Claim creation finalized in [0x9c1e7392bb72ec0b1b94cbd5dd9a17604187634b005b8bcaf62b0215e3db4aff](https://explorer-studio.genlayer.com/tx/0x9c1e7392bb72ec0b1b94cbd5dd9a17604187634b005b8bcaf62b0215e3db4aff), evidence submission in [0x414c2e4cbc752d3b2a239cfd6cb1b6fc1f5fd3e565d4063dae5cf5332515a962](https://explorer-studio.genlayer.com/tx/0x414c2e4cbc752d3b2a239cfd6cb1b6fc1f5fd3e565d4063dae5cf5332515a962), verification in [0xc86e44e2f4bd1ce6d4a5fec3b0f4d1cf23d15dc50add1e4208db513bb99dae2d](https://explorer-studio.genlayer.com/tx/0xc86e44e2f4bd1ce6d4a5fec3b0f4d1cf23d15dc50add1e4208db513bb99dae2d), and its 1-wei bounty in [0x8bca3c75f2e48523eb5eb0c3e2ab412df157281eec178bedda3341d875c5cd68](https://explorer-studio.genlayer.com/tx/0x8bca3c75f2e48523eb5eb0c3e2ab412df157281eec178bedda3341d875c5cd68). The verification history timestamp is `1790542056`, and `get_freshness` returned `FRESH`. A conservative safe-to-test time based on that history timestamp is `2026-10-04 20:47:37 UTC`; then read freshness/status and record sponsor refund settlement.

The canonical 30-day bounty timeout has not elapsed live. A 60-second timeout probe exists on a separate, superseded-source deployment and is not proof of the hardened contract's full timeout path. The earlier live unavailable-source, relationship-conflict, evidence-free challenge, pinned-digest mismatch, and graph-supersession tests were run at predecessor address [0x4512b07d637Fe42D278B25dd778a9a1a99A38Dbb](https://explorer-studio.genlayer.com/address/0x4512b07d637Fe42D278B25dd778a9a1a99A38Dbb); they are retained as historical evidence and do not count as live verification of the new source. Prompt injection and the maximum-size state path remain Direct Mode-only.

## Local validation

- `python -m pytest -q`: 70 passed.
- `gltest tests --network localnet`: 70 passed.
- `genvm-lint check contracts/provenance_engine.py`: 3 checks passed.
- `genvm-lint schema contracts/provenance_engine.py`: 14 methods (8 views and 6 writes).

See [deployment evidence](DEPLOYMENT_EVIDENCE.md) for full historical transaction details. The predecessor source SHA-256 was `F945961C305652E41064CD119B3E015DCEBDC1F2CE17A19DB013F474D0F324B4`; its deployed tests apply to that source only.
