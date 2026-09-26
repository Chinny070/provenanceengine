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

Record the finalized transaction ID, contract address, and SHA-256 source hash in `deployments/studionet.json` (ignored by Git because it is generated evidence).
