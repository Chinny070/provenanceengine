# Consensus design

`verify_claim` performs an independent HTML render and a semantic evidence classification inside `gl.vm.run_nondet_unsafe`. This follows the current GenLayer nondeterminism and web-render APIs: [non-determinism](https://docs.genlayer.com/developers/intelligent-contracts/features/non-determinism), [web access](https://docs.genlayer.com/developers/intelligent-contracts/features/web-access), and [calling LLMs](https://docs.genlayer.com/developers/intelligent-contracts/features/calling-llms).

The leader and validator each:

1. Render the stored HTTPS URL with `gl.nondet.web.render(..., mode="html")`.
2. Hash the exact UTF-8 HTML and normalized visible text; derive the versioned evidence identity from those hashes and the immutable claim/source context.
3. Ask for structured JSON containing exactly `decision`, `graph_relationship`, and `sufficient`. The claim decision is `SUPPORTS`, `CONTRADICTS`, or `INSUFFICIENT`; graph relation is `NONE`, `SUPERSEDES`, `EXPIRES`, or `RESTORES`; sufficiency is an exact boolean.
4. Treat the rendered page as hostile data and ignore instructions embedded in it.

The validator re-renders and reclassifies independently. It rejects malformed shapes, extra keys, wrong types, unknown enums, changed hashes or identity, and any difference in reachability, semantic decision, graph relation, or sufficiency. It does not trust a leader-produced hash merely because the value is well-formed. The deterministic path validates the digest and recomputes the evidence identity before changing state.

The submitter relationship is an assertion included for context only. The consensus `decision` determines whether that evidence supports or contradicts the claim. A separate consensus `graph_relationship` creates an edge only when a target evidence item is bound in the call and the validators agree on the relation to that target.

Consensus is intentionally strict about hash equality. Pages that vary by time, personalization, or validator rendering may fail to reach a final result; the contract does not downgrade that failure to support. Protocol-level `UNDETERMINED` remains possible. `UNAVAILABLE` is accepted only when the render itself is unavailable or empty; it is never treated as contradiction.

After consensus, ordinary deterministic code stores the observed hashes, identity, typed finding, timestamp, receipt, and optional graph edge. The contract derives aggregate claim status from active evidence rather than accepting a model's final-state answer. No visual decision or screenshot is present in the output schema.
