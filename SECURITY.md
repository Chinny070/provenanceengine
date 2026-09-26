# Security model

Provenance Engine treats URLs, fetched pages, hashes, evidence metadata, and model output as untrusted input.

- Bounded IDs, text, URLs, hashes, per-claim evidence, and history prevent unbounded state growth.
- Relationship, retrieval, consensus, and visual values are closed enums.
- The verifier prompt explicitly treats fetched content as data rather than instructions.
- Only a parsed, exact JSON schema crosses the consensus boundary.
- Claim and evidence IDs are single-use, preventing replay and identity replacement.
- Evidence belongs to exactly one claim, checked before verification or challenge.
- Claims preserve append-only history rather than deleting contradictory evidence.
- Bounties accept only `gl.message.value`; fee deposits are never counted as escrow.
- Payouts zero the stored amount and mark settlement before emitting a transfer, preventing duplicate payout on replay.
- Inconclusive verification retains the bounty for a later retry. Contradicted, unavailable, or stale claims refund the sponsor; confirmed claims pay the caller who completes settlement.

The contract does not claim that a supplied content hash proves a publisher's identity. It preserves the declared identity and makes the validator observation and consensus receipt auditable.
