# Provenance Engine

Provenance Engine is a standalone GenLayer Intelligent Contract for recording claims, independently rendering public web evidence, and preserving validator-agreed findings and evidence evolution on-chain.

The trust question is narrow: **does the independently rendered public source substantively support or contradict this immutable claim, and does it establish a relation to a specific earlier evidence item?** GenLayer is load-bearing because the leader and validator each render the source and independently classify the evidence; deterministic contract code checks their typed outputs before it records the finding or changes a bounty.

This repository contains the contract and its tests. It has no frontend and is not an application. The only implemented retrieval class is `RENDER_HTML`; screenshots, visual classification, arbitrary APIs, manual attestations, and multi-source independence are not claimed.

## Evidence and state

`submit_evidence` accepts a client alias, claim ID, HTTPS URL, a submitter relationship assertion, and an optional target evidence alias. It does not accept content hashes or render hashes. `verify_claim` independently calls `gl.nondet.web.render(url, mode="html")` in the nondeterministic block. It derives:

- `render_hash = SHA256(UTF8(exact rendered HTML))`.
- `content_hash = SHA256(UTF8(lowercase, whitespace-collapsed visible HTML text after removing comments, tags, script, and style blocks))`.
- `evidence_id = SHA256(UTF8(compact JSON array ["provenance-evidence-v1", claim_id, source_url, "RENDER_HTML", render_hash, content_hash, "html-text-whitespace-lower-v1"]))`.

The server-derived evidence ID is separate from the caller's alias. A repeated observation of identical claim, URL, and artifact has the same canonical evidence ID. Validator observation time and submitter identity are recorded by the contract, never supplied by the model.

Claim status is derived from active evidence at read and settlement time. Fresh support plus fresh contradiction is `DISPUTED`; support alone is `CONFIRMED`; contradiction alone is `CONTRADICTED`; no decisive active finding with old evidence is `STALE`; unavailable-only evidence is `UNAVAILABLE`; otherwise the result is `INSUFFICIENT`. Evidence older than seven days is excluded from the active semantic result. Freshness is `FRESH` through one day, `AGING` through seven days, then `STALE`.

For targeted evidence evolution, each observation is separately classified against the claim and, when a target is supplied, against that specific prior evidence item. A consensus-backed `SUPERSEDES` or `EXPIRES` edge deactivates its target; a later `RESTORES` edge reactivates the target. Edges are append-only, capped, and point to an earlier evidence item, preventing cycles. The provenance passport exposes claim and evidence digests, support and contradiction counts, graph digest, consensus receipt digest, freshness, and version.

## Consensus and limitations

The leader returns typed fields for reachability, claim decision, graph relationship, sufficiency, and independently derived artifact hashes and identity. The validator repeats the render and classification and rejects differences in any returned field. Model prose is not stored. A rendered-page source that changes between validators may cause consensus not to finalize; the contract fails closed. Retrieved instructions are treated as hostile data, but LLM classification remains semantic judgment rather than cryptographic proof of truth or publisher identity.

Only one source is needed for `CONFIRMED`; the contract does not establish publisher independence or protection from syndicated sources. HTTPS URL admission rejects credentials, explicit ports, IP literals, malformed hosts, and local-name suffixes. The contract cannot itself prove how a public DNS name resolves at the validator's network boundary. No image is supplied or classified, so visual provenance is intentionally absent.

Challenges require an already verified conflicting finding and one of three fixed reason codes; they cannot clear or overwrite a finding. There is no separate challenge appeal or bond mechanism. A bounty pays the submitter of the first active, non-stale supporting evidence item, regardless of who calls settlement. A contradicted, unavailable, or stale claim refunds the sponsor; a disputed or unresolved bounty can be permissionlessly refunded after 30 days. State is cleared before the sole transfer helper runs. See [SECURITY.md](SECURITY.md) for the complete policy and remaining limits.

## Public contract surface

Writes: `create_claim`, `submit_evidence`, `verify_claim`, `challenge_claim`, `create_bounty`, `claim_reward`.

Views: `get_claim`, `get_evidence`, `get_evidence_edges`, `get_history`, `get_bounty`, `get_provenance_passport`, `get_status`, `get_freshness`.

## Verification

The contract targets the pinned `py-genlayer` runner in its first-line `Depends` declaration. The current official APIs used here are `gl.nondet.web.render`, `gl.nondet.exec_prompt(..., response_format="json")`, and `gl.vm.run_nondet_unsafe` with an independent substantive validator.

```powershell
python -m pytest -q
genvm-lint check contracts/provenance_engine.py
genvm-lint schema contracts/provenance_engine.py
gltest tests -v --network localnet
```

Current local test output is tracked in [release-candidate verification](docs/RELEASE_CANDIDATE_VERIFICATION.md). A source change makes the prior deployment non-parity evidence until a new contract is deployed and live-tested. Deployment instructions are in [DEPLOYMENT.md](DEPLOYMENT.md).

## Example consumers

- **AI-agent commerce:** bind a work-completion claim to public evidence; a marketplace reads the passport before releasing a separate payment.
- **Policy monitoring:** register a public rule, preserve later `SUPERSEDES` or `EXPIRES` findings, and let downstream governance query the evolution graph.
- **Decentralized insurance:** preserve a claimant's evidence and later contradictions; a claims process can read the consensus receipt and status without replacing its own policy logic.

These are integration patterns. The contract does not implement a marketplace, governance application, insurance product, or consumer-specific rules.
