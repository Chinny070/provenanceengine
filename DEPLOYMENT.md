# Deployment evidence

Deploy only after the checks in the README pass.

1. Select `studionet` and run `genlayer network info`; save its effective chain and RPC details.
2. Run `genvm-lint check` and `genvm-lint schema` against the exact contract file.
3. Run direct-mode tests and a live Studionet deployment.
4. Wait for finalization before recording the contract address.
5. Calculate the source hash with `Get-FileHash contracts/provenance_engine.py -Algorithm SHA256`.
6. After finality, record the active address, deployment transaction, source hash, and transaction IDs in `deployments/studionet.json`; keep the human-readable results in `docs/DEPLOYMENT_EVIDENCE.md`.

The manifest is updated only after finalization so it cannot imply a deployment that did not happen. When a corrected source is redeployed, mark earlier addresses as superseded and keep the active address at the top level.
