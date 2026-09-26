# Deployment evidence

Deploy only after the checks in the README pass.

1. Select `studionet` and run `genlayer network info`; save its effective chain and RPC details.
2. Run `genvm-lint check` and `genvm-lint schema` against the exact contract file.
3. Run direct-mode tests and a live Studionet deployment.
4. Wait for finalization before recording the contract address.
5. Calculate the source hash with `Get-FileHash contracts/provenance_engine.py -Algorithm SHA256`.
6. Store address, deployment transaction, source hash, timestamp, and verification transaction IDs in the ignored generated deployment manifest.

The live manifest is deliberately generated after finalization so it cannot imply a deployment that did not happen.
