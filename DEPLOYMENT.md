# Studionet deployment and release procedure

The current source is pushed to `main` at commit `d35478517283377508b44521516355c67f82f6ff` and deployed on Studionet at [0xbC3fE4d84CE9b9E57c491494De62dbD999Ca4aC2](https://explorer-studio.genlayer.com/address/0xbC3fE4d84CE9b9E57c491494De62dbD999Ca4aC2). Deployment transaction [0xe15faf459e161b7ba5d7e76267dbfd33ab37261674e31d99333de5473190cdec](https://explorer-studio.genlayer.com/tx/0xe15faf459e161b7ba5d7e76267dbfd33ab37261674e31d99333de5473190cdec) finalized with `MAJORITY_AGREE`. The source read back from Studionet hashes to `40A94B3192EBC5957620528714128D0295F4A81288E2AE8E33E1F354C41E4BEB`, matching the 790-line committed contract exactly. Local checks and a live rendered-evidence confirmation, pinned-text verification history, rejected-payable refund message, bounty payout to the submitter, and replay attempt are recorded in [deployment evidence](docs/DEPLOYMENT_EVIDENCE.md). The release is not submission-ready: live contradiction is verified, but disputed aggregate state, graph edges, conclusive unavailable-source behavior, challenge, prompt-injection fixture, and timeout recovery remain unverified.

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
- Current Studionet deployment and transaction are recorded in `deployments/studionet.json`; its source hash matches the committed source.
- A later pre-addendum checkpoint at `0x2827dB51F877691e0001De9Fc8A29e3Ba1Ea69CA` used source SHA-256 `0A90EFCF909B3075BB1D2398A9FBEF176F625009CE419FC262C631E2D797B478`. It was retired after a live empty-argument coercion failure; see the deployment evidence log.

See [release-candidate verification](docs/RELEASE_CANDIDATE_VERIFICATION.md) for the current local checks and unresolved live gate.
