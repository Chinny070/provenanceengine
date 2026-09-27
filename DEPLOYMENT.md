# Studionet deployment and release procedure

The current hardened source is deployed on Studionet at [0xD745F2093De04c4C3c9a4648981D5b6d880163d0](https://explorer-studio.genlayer.com/address/0xD745F2093De04c4C3c9a4648981D5b6d880163d0). Deployment transaction [0xfaff041e78eb4e25b574010a3694e8c35fa8b9d22a871a5a93eb6f14218b4617](https://explorer-studio.genlayer.com/tx/0xfaff041e78eb4e25b574010a3694e8c35fa8b9d22a871a5a93eb6f14218b4617) finalized with `MAJORITY_AGREE`; deployed-source read-back matches SHA-256 `ED6D31F4668D5E6BB9494E5F1266F79157F968AAF904FD1A1597188A70083E41`. Scoped quota regressions and worst-case Direct Mode reads/settlement pass. A current-source live confirmation and 1-wei bounty payout are recorded in [deployment evidence](docs/DEPLOYMENT_EVIDENCE.md). A fresh stale-evidence case is staged on this address until `2026-10-04 20:47:37 UTC`; the canonical 30-day timeout and several adversarial paths have not been live-tested on this source.

## Before deployment

1. Fetch the latest `main`; preserve the rollback branch created before hardening.
2. Run `python -m pytest -q`, `genvm-lint check contracts/provenance_engine.py`, and `genvm-lint schema contracts/provenance_engine.py`.
3. Run `gltest tests -v --network localnet` and inspect every failure.
4. Commit and push the tested source and docs.
5. Confirm the installed GenLayer CLI version, active signer address, target network, and sufficient GEN balance. Do not print or store signing secrets.
6. Set Studionet and confirm chain ID `61999` before deploying.
7. Deploy exactly `contracts/provenance_engine.py`; wait for a finalized receipt and verify the resulting contract address in the explorer.
8. Read back schema and representative contract state. Compare the deployed source hash with `Get-FileHash contracts/provenance_engine.py -Algorithm SHA256` and record the release commit.
9. Finish the remaining live gates: disputed aggregate state, graph evolution, conclusive unavailable-source behavior, challenge, prompt-injection fixture, timeout recovery, and any cross-wallet beneficiary check. Record only finalized receipts and observed state.
10. Update `deployments/studionet.json`, `docs/DEPLOYMENT_EVIDENCE.md`, and this file only after their corresponding live receipts finalize. Then rerun checks, commit, and push the final evidence.

## Current verified baseline

- CLI version observed locally: `0.39.1`.
- GenVM linter: `0.11.1rc2`.
- Python Direct Mode test plugin: `genlayer-test 0.29.2`.
- Contract runner dependency currently pinned in source: `py-genlayer:1jb45aa8ynh2a9c9xn3b7qqh8sm5q93hwfp7jqmwsfhh8jpz09h6`, shown by the current official [first-contract documentation](https://docs.genlayer.com/developers/intelligent-contracts/first-contract) and [storage documentation](https://docs.genlayer.com/developers/intelligent-contracts/storage). The linter cache advertises `5jycge4q8k23462jtb0b9fyey1s9qz928sz2nbrd9mg4sxqg2qng` as newer, but both lint validation and schema loading with that candidate fail because `allow_storage` is undefined. Keep the documented compatible runner pin until the SDK/API migration is verified.
- Current Studionet deployment is `0xD745F2093De04c4C3c9a4648981D5b6d880163d0`; transaction and source parity are recorded in `deployments/studionet.json`.
- A later pre-addendum checkpoint at `0x2827dB51F877691e0001De9Fc8A29e3Ba1Ea69CA` used source SHA-256 `0A90EFCF909B3075BB1D2398A9FBEF176F625009CE419FC262C631E2D797B478`. It was retired after a live empty-argument coercion failure; see the deployment evidence log.

See [release-candidate verification](docs/RELEASE_CANDIDATE_VERIFICATION.md) for current local checks and unresolved live gates.
