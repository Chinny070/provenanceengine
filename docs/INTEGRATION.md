# Integration notes

Consumers use the contract directly; no frontend or project-specific deployment is required.

1. Call `create_claim(claim_id, statement)` once. The claim definition is immutable.
2. Call `submit_evidence(alias, claim_id, https_url, relationship_assertion[, target_alias])`. Use `SUPPORTS` or `CONTRADICTS` for claim evidence. Use `SUPERSEDES`, `EXPIRES`, or `RESTORES` plus a verified prior target when asking validators to evaluate an evolution edge. The labels are assertions, not authority.
3. Call `verify_claim(claim_id, alias)` and wait for a finalized receipt. Validators render the URL and derive the canonical evidence identity.
4. Read `get_status`, `get_freshness`, `get_evidence`, `get_evidence_edges`, `get_history`, or `get_provenance_passport`.
5. If a bounty exists, anyone can call `claim_reward`. The beneficiary is determined by evidence and claim status, not by that caller.

Example consumer patterns:

- An AI-agent commerce contract records a work claim and lets its payment flow read a provenance passport before its own release rule.
- A governance monitor records an agency statement and later attaches a consensus-backed expiration or supersession edge.
- An insurance process preserves initial evidence and later contradictions, then reads the resulting dispute status.

The consumers retain their own authorization, policy, payment, and dispute logic. Provenance Engine does not attest that a publisher is authoritative, cluster syndicated sources, or decide downstream application policy.
