# Integration notes

Consumers use the contract directly; no frontend or project-specific deployment is required.

1. Call `create_claim(claim_id, statement)` once. The claim definition is immutable.
2. Call `submit_evidence(alias, claim_id, https_url, relationship_assertion, evidence_class, expected_digest, target_alias)`. Use `RENDERED_WEB`, `NONE`, `NONE` for the final three fields for ordinary rendered evidence. Use `PINNED_TEXT`, the lowercase SHA-256 of exact response bytes, and `NONE` for pinned text. The CLI caller must pass the literal `NONE` for absent values because a bare empty argument is parsed as numeric zero. Pinned text is lower assurance and cannot produce `CONFIRMED` or a bounty payout. Graph assertions `SUPERSEDES`, `EXPIRES`, or `RESTORES` require a prior verified rendered-evidence target.
3. Call `verify_claim(claim_id, alias)` and wait for a finalized receipt. Validators independently fetch and hash pinned text or rerender rendered web evidence, then derive the canonical identity and classification.
4. Read `get_status`, `get_freshness`, `get_evidence`, `get_evidence_edges`, `get_history`, or `get_provenance_passport`.
5. If a bounty exists, anyone can call `claim_reward`. The beneficiary is determined by evidence and claim status, not by that caller.

Example consumer patterns:

- An AI-agent commerce contract records a work claim and lets its payment flow read a provenance passport before its own release rule.
- A governance monitor records an agency statement and later attaches a consensus-backed expiration or supersession edge.
- An insurance process preserves initial evidence and later contradictions, then reads the resulting dispute status.

The consumers retain their own authorization, policy, payment, and dispute logic. Provenance Engine does not attest that a publisher is authoritative, cluster syndicated sources, or decide downstream application policy.
