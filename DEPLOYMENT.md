# Studionet deployment and release procedure

The repository currently contains a hardened source version that has not yet been deployed. The address in `deployments/studionet.json` is retained as historical evidence for an earlier source hash and is not evidence for the current working tree. Do not call this source release-ready until a new deployment, live tests, and source parity are recorded.

## Before deployment

1. Fetch the latest `main`; preserve the rollback branch created before hardening.
2. Run `python -m pytest -q`, `genvm-lint check contracts/provenance_engine.py`, and `genvm-lint schema contracts/provenance_engine.py`.
3. Run `gltest tests -v --network localnet` and inspect every failure.
4. Commit and push the tested source and docs.
5. Confirm the installed GenLayer CLI version, active signer address, target network, and sufficient GEN balance. Do not print or store signing secrets.
6. Set Studionet and confirm chain ID `61999` before deploying.
7. Deploy exactly `contracts/provenance_engine.py`; wait for a finalized receipt and verify the resulting contract address in the explorer.
8. Read back schema and representative contract state. Compare the deployed source hash with `Get-FileHash contracts/provenance_engine.py -Algorithm SHA256` and record the release commit.
9. Run live claim creation, rendered evidence verification, conflicting finding, graph evolution, challenge record, bounty payout, replay, contradiction refund, stale settlement, and timeout recovery. Save only finalized transaction hashes and observed results.
10. Exercise rejected payable input with a tiny amount and record sender and contract balances before/after, including any asynchronous EVM transfer receipt. Then test bounty deposit, correct beneficiary payout, refund, and replay.
11. Update `deployments/studionet.json`, `docs/DEPLOYMENT_EVIDENCE.md`, and this file only after their corresponding live receipts finalize. Then rerun tests, commit, and push the final evidence.

## Current verified baseline

- CLI version observed locally: `0.39.1`.
- GenVM linter: `0.11.1rc2`.
- Python Direct Mode test plugin: `genlayer-test 0.29.2`.
- Contract runner dependency currently pinned in source: `py-genlayer:1jb45aa8ynh2a9c9xn3b7qqh8sm5q93hwfp7jqmwsfhh8jpz09h6`, shown by the current official [first-contract documentation](https://docs.genlayer.com/developers/intelligent-contracts/first-contract) and [storage documentation](https://docs.genlayer.com/developers/intelligent-contracts/storage). The linter cache advertises `5jycge4q8k23462jtb0b9fyey1s9qz928sz2nbrd9mg4sxqg2qng` as newer, but both lint validation and schema loading with that candidate fail because `allow_storage` is undefined. Keep the documented compatible runner pin until the SDK/API migration is verified.
- Last documented Studionet deployment: `0xA77018a83C4d353eF31E07B59A2Ff50153e1264a`, source hash `81B81B6E5CB57875236719D1A762791D94FA5C30C8ED0E24B92CC51C27AFBCF6`. This hash does not match the hardened working tree.
- A later pre-addendum checkpoint at `0x2827dB51F877691e0001De9Fc8A29e3Ba1Ea69CA` used source SHA-256 `0A90EFCF909B3075BB1D2398A9FBEF176F625009CE419FC262C631E2D797B478`. It was retired after a live empty-argument coercion failure; see the deployment evidence log.

See [release-candidate verification](docs/RELEASE_CANDIDATE_VERIFICATION.md) for the current local checks and unresolved live gate.
