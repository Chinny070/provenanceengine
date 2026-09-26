# Provenance Engine

Provenance Engine is a reusable GenLayer Intelligent Contract primitive for turning external claims and evidence into consensus-backed historical records.

It has no frontend or application layer. Applications call this primitive to register claims, append evidence lineage, obtain validator-backed outcomes, preserve contradictions, track freshness, and expose a reusable provenance passport.

## What it records

- Immutable claim identities and append-only audit history.
- Evidence identity: URL, retrieval mode, content hash, render hash, relationship, submitter, and timestamp.
- Consensus outcomes that are schema-gated before any state update.
- Contradiction and visual classifications without destroying earlier evidence.
- Freshness as `FRESH`, `AGING`, `STALE`, or `UNKNOWN`.
- Escrowed verification bounties with replay-safe settlement.

## Contract interface

Writes: `create_claim`, `submit_evidence`, `verify_claim`, `challenge_claim`, `create_bounty`, `claim_reward`.

Views: `get_claim`, `get_evidence`, `get_history`, `get_provenance_passport`, `get_status`, `get_freshness`.

See [CONSENSUS.md](CONSENSUS.md), [SECURITY.md](SECURITY.md), and [DEPLOYMENT.md](DEPLOYMENT.md) before deployment.

## Local verification

```powershell
python -m pip install -r requirements.txt
python -m pytest -q
genvm-lint check contracts/provenance_engine.py
genvm-lint schema contracts/provenance_engine.py
gltest tests -v --network localnet
```

## Deploy to Studionet

```powershell
genlayer network set studionet
genlayer network info
genlayer deploy --contract contracts/provenance_engine.py
```

The current Studionet deployment is [0xA77018a83C4d353eF31E07B59A2Ff50153e1264a](https://explorer-studio.genlayer.com/address/0xA77018a83C4d353eF31E07B59A2Ff50153e1264a). Its finalized deployment transaction, source hash, and live lifecycle and escrow results are recorded in [`deployments/studionet.json`](deployments/studionet.json) and [`docs/DEPLOYMENT_EVIDENCE.md`](docs/DEPLOYMENT_EVIDENCE.md).
