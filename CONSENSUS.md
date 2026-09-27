# Consensus design

`verify_claim` uses the evidence primitive selected at registration, followed by substantive semantic classification inside `gl.vm.run_nondet_unsafe`. This follows the current GenLayer nondeterminism, web access, and structured LLM APIs: [non-determinism](https://docs.genlayer.com/developers/intelligent-contracts/features/non-determinism), [web access](https://docs.genlayer.com/developers/intelligent-contracts/features/web-access), and [calling LLMs](https://docs.genlayer.com/developers/intelligent-contracts/features/calling-llms).

For `RENDERED_WEB`, the leader and validator each:

1. Render the stored HTTPS URL with `gl.nondet.web.render(..., mode="html")`.
2. Hash the exact UTF-8 HTML and normalized visible text; derive the versioned evidence identity from those hashes and the immutable claim/source context.
3. Ask for structured JSON containing exactly `decision`, `graph_relationship`, and `sufficient`. The claim decision is `SUPPORTS`, `CONTRADICTS`, or `INSUFFICIENT`; graph relation is `NONE`, `SUPERSEDES`, `EXPIRES`, or `RESTORES`; sufficiency is an exact boolean.
4. Treat rendered content as hostile data and ignore instructions embedded in it.

For `PINNED_TEXT`, the leader and validator each call `gl.nondet.web.get`, require a successful response with bounded nonempty bytes, recompute SHA-256 over those exact bytes, and compare it with the immutable pin assertion. A mismatch becomes `INTEGRITY_MISMATCH` and does not reach the semantic model. Matching bytes must decode as UTF-8 before the model evaluates the text. A pinned-text result is lower assurance: it contributes `TEXT_ONLY`, never `CONFIRMED`, `CONTRADICTED`, or bounty eligibility.

The validator repeats retrieval/rendering, hash derivation, and semantic classification independently. It rejects malformed shapes, extra keys, wrong types, unknown enums, changed hashes or identity, and any difference in reachability, semantic decision, graph relation, or sufficiency. It does not trust a leader-produced hash merely because the value is well-formed. The deterministic path validates the digest and recomputes the evidence identity before changing state.

The submitter relationship is an assertion included for context only. The consensus `decision` determines whether that evidence supports or contradicts the claim. A separate consensus `graph_relationship` creates an edge only when a target evidence item is bound in the call and the validators agree on the relation to that target.

Consensus is intentionally strict about hash equality. Sources that vary by time, personalization, or validator rendering may fail to reach a final result; the contract does not downgrade that failure to support. Protocol-level `UNDETERMINED` remains possible. A fetch/render unavailable or empty result is `UNAVAILABLE`; a pinned digest mismatch is `INTEGRITY_MISMATCH`. Neither is treated as contradiction.

After consensus, ordinary deterministic code stores the observed hashes, identity, typed finding, timestamp, receipt, and optional graph edge. The contract derives aggregate claim status from active evidence rather than accepting a model's final-state answer. `PINNED_JSON` and visual evidence are not exposed in the output schema.
